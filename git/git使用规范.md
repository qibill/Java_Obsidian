# 分支管理

*   **master**：整个项目主分支，有且仅有一个，除项目负责人以外的开发人员不能像master分支合并内容。

*   **develop**：主分支只用来分布重大版本，日常开发应该在另一条分支上完成。开发用的分支为Develop。

*   **feature**：feature是为了开发后续版本的功能，从Develop分支上面分出来的。开发完成稳定后，要再并入Develop。

*   **release**: release是发布正式版本之前（即合并到Master分支之前），我们可能需要有一个预发布的版本进行测试。

*   **fixbug**：fixbug分支是从master分支上面分出来的。fix结束以后，再合并进Master和Develop分支。最后，删除"fixbug分支"。

![分支管理](https://i-blog.csdnimg.cn/blog_migrate/d8b418517a03a0cff54cf8bd94be3d49.png)
