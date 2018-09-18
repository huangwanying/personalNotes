# JDBC

## Icebreaker

### Java Database Connectivity

+ Database
  - MySQL
  - Oracle
  - SQLServer

+ 统一的数据库访问规则

### JDBC Basic step

+ 注册驱动

  `DriverManager.registerDriver(new com.mysql.jdbc.Driver());`

+ 建立连接。参数一：协议+访问的数据库；参数二：用户名；参数三：密码。

  `Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/student","root"."root");`

+ 创建statement，跟数据库打交道，一定需要这个对象

  `Statement st = conn.createStatement();`

+ 执行查询，得到结果

  `String sql = "select * from t_stu";`

  `rs = st.executeQuery(sql);`

+ 遍历结果集，查询每一条记录

  ```java
  while(rs.next()){
  	int id = rs.getInt("id");
  	String name = rs.getString("name");
  	int age = rs.getInt("age");
      System.out.println("id="+id + "===name="+name+"==age="+age);			
  }
  ```

+ 释放资源

  ```java
  try {
      if(rs != null){
          rs.close();
      }
  } catch (SQLException e) {
      e.printStackTrace();
  }finally{
      rs = null;
  }
  ```

### JDBC工具类构建

+ 资源释放工作的整合

+ 驱动防二次注册

  `Class.forName("com.mysql.jdbc.Driver");`

  Driver这个类里面有静态代码块（类加载了就执行），一上来就执行了`DriverManager.registerDriver(new com.mysql.jdbc.Driver());`，所以等同于我们注册了两次驱动，其实没这个必要。