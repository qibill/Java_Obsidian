## 把Job的实例交给Spring管理

   - Quartz提供了JobFactory接口，让我们可以自定义实现创建Job的逻辑。
   - 通过实现JobFactory接口，在实例化Job以后，在通过ApplicationContext将Job所需要的属性注入即可。
   - 在Spring与Quartz集成时，用到了org.springframework.scheduling.quartz.SchedulerFactoryBean这个类。


```
// Get Scheduler instance from SchedulerFactory.
        try {
            this.scheduler = createScheduler(schedulerFactory, this.schedulerName);
            populateSchedulerContext();
 
            if (!this.jobFactorySet && !(this.scheduler instanceof RemoteScheduler)) {
                // Use AdaptableJobFactory as default for a local Scheduler, unless when
                // explicitly given a null value through the "jobFactory" bean property.
              |--  this.jobFactory = new AdaptableJobFactory(); --|
                  //这里是重点。
            }
            if (this.jobFactory != null) {
                if (this.jobFactory instanceof SchedulerContextAware) {
                    ((SchedulerContextAware) this.jobFactory).setSchedulerContext(this.scheduler.getContext());
                }
                this.scheduler.setJobFactory(this.jobFactory);
            }
        }
```

- 由于将Scheduler交给Spring生成， SchedulerFactoryBean有个jobFactory属性 而且jobFactory是实现SchedulerContextAware的类还要继承AdaptableJobFactory。
- 在Spirng-context-support jar包下org.springframework.scheduling.quartz包中有个SpringBeanJobFactory的类继承了AdaptableJobFactory实现AdaptableJobFactory，spring会默认使用这个给jobFactory，我们可以继承SpringBeanJobFactory重写他的createJobInstance方法

```
import org.quartz.SchedulerContext;
import org.quartz.spi.TriggerFiredBundle;
import org.springframework.beans.BeanWrapper;
import org.springframework.beans.MutablePropertyValues;
import org.springframework.beans.PropertyAccessorFactory;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.lang.Nullable;
import org.springframework.scheduling.quartz.SpringBeanJobFactory;

/**
 * @author qibill
 * @date 2020/5/11 11:22
 **/
public class JobBeanFactory extends SpringBeanJobFactory {

    @Nullable
    private String[] ignoredUnknownProperties;

    @Nullable
    private SchedulerContext schedulerContext;


    private final BeanFactory beanFactory;


    public JobBeanFactory(BeanFactory beanFactory) {
        this.beanFactory = beanFactory;
    }


    @Override
    public void setIgnoredUnknownProperties(String... ignoredUnknownProperties) {
        this.ignoredUnknownProperties = ignoredUnknownProperties;
    }

    @Override
    public void setSchedulerContext(SchedulerContext schedulerContext) {
        this.schedulerContext = schedulerContext;
    }


    @Override
    protected Object createJobInstance(TriggerFiredBundle bundle) throws Exception {
        Class<?> jobClass = bundle.getJobDetail().getJobClass();
        Object job = beanFactory.getBean(jobClass);
        if (isEligibleForPropertyPopulation(job)) {
            BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(job);
            MutablePropertyValues pvs = new MutablePropertyValues();
            if (this.schedulerContext != null) {
                pvs.addPropertyValues(this.schedulerContext);
            }
            pvs.addPropertyValues(bundle.getJobDetail().getJobDataMap());
            pvs.addPropertyValues(bundle.getTrigger().getJobDataMap());
            if (this.ignoredUnknownProperties != null) {
                for (String propName : this.ignoredUnknownProperties) {
                    if (pvs.contains(propName) && !bw.isWritableProperty(propName)) {
                        pvs.removePropertyValue(propName);
                    }
                }
                bw.setPropertyValues(pvs);
            } else {
                bw.setPropertyValues(pvs, true);
            }
        }
        return job;
    }
}
```

当Spring在加载配置文件时，如果配置文件中有Bean实现了ApplicationContextAware接口时,Spring会自动调用setApplicationContext方法,我们可以通过这个获取Spring上下文然后在创建Job时让Job自动注入到Spring容器中


```
import com.qibill.factory.JobBeanFactory;
import com.qibill.job.QuartzJob;
import org.quartz.*;
import org.springframework.beans.BeansException;
import org.springframework.boot.autoconfigure.quartz.SchedulerFactoryBeanCustomizer;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author qibill
 * @date 2019/8/29 11:27
 **/
@Configuration
public class QuartzConfig implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    /**
     * 使用jobDetail包装job
     * @return
     */
    @Bean
    public JobDetail zhiyeSampleJobDetail() {
        return JobBuilder.newJob(QuartzJob.class).withIdentity("ZhiyeSampleJob").storeDurably().build();
    }

    /**
     * 把jobDetail注册到trigger上去
     * @return
     */
    @Bean
    public Trigger sampleJobTrigger() {
        CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule("0 0 0 * * ?");
        return TriggerBuilder.newTrigger()
                .forJob(zhiyeSampleJobDetail())
                .withIdentity("sampleJobTrigger")
                .withSchedule(cronScheduleBuilder)
                .build();
    }

    @Bean
    public SchedulerFactoryBeanCustomizer schedulerFactoryBeanCustomizer() {
        return schedulerFactoryBean -> schedulerFactoryBean.setJobFactory(new JobBeanFactory(applicationContext));
    }


    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
```

Job也需要加上注解@Component

```
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.quartz.QuartzJobBean;
import org.springframework.stereotype.Component;

/**
 * QuartzJob
 *
 * @author qibill
 * @date 2019/8/29 11:37
 **/
@Component
public class QuartzJob extends QuartzJobBean {

    private static final Logger LOGGER = LoggerFactory.getLogger(QuartzJob.class);

    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        LOGGER.info("定时任务");
    }
}
```
