
```java
public class jdbc {
    public static void main(String[] args) {
    	Connection connection = null;
    	PreparedStatement preparedStatement = null;
    	ResultSet resultSet = null;
    	
    	try {
    		//1、加载数据库驱动
    		Class.forName("com.mysql.jdbc.Driver");
    		//2、通过驱动管理类获取数据库链接
    		connection =  DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8", "root", "root");
    		//3、定义sql语句 ?表示占位符
    	    String sql = "select * from user where username = ?";
    		//4、获取预处理statement
    		preparedStatement = connection.prepareStatement(sql);
    		//5、设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
    		preparedStatement.setString(1, "王五");
    		//6、向数据库发出sql执行查询，查询出结果集
    		resultSet =  preparedStatement.executeQuery();
    		//7、遍历查询结果集
    		while(resultSet.next()){
    			User user ;
    			System.out.println(resultSet.getString("id")+"  "+resultSet.getString("username"));
    		}
    	} catch (Exception e) {
    		e.printStackTrace();
    	}finally{
    		//8、释放资源
    		if(resultSet!=null){
    			try {
    				resultSet.close();
    			} catch (SQLException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    		if(preparedStatement!=null){
    			try {
    				preparedStatement.close();
    			} catch (SQLException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    		if(connection!=null){
    			try {
    				connection.close();
    			} catch (SQLException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    
    	}
    
    }
}
```
