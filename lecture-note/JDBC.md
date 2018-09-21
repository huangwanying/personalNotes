#  JDBC

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

### JDBCUtil.java

+ 资源释放工作的整合

+ 驱动防二次注册

  使用这种方式即可

  `Class.forName("com.mysql.jdbc.Driver");`

  不使用

  `DriverManager.registerDriver(new com.mysql.jdbc.Driver());`

  Driver这个类里面有静态代码块（类加载了就执行），一上来就执行了，所以等同于我们注册了两次驱动，其实没这个必要。

+ 使用properties配置文件

  + 在src底下声明一个文件 xxx.properties，里面的内容：

    driverClass=com.mysql.jdbc.Driver
    url=jdbc:mysql://10.16.74.136:3306/ibcp_dev?characterEncoding=UTF8&zeroDateTimeBehavior=convertToNull&transformedBitIsBoolean=true&allowMultiQueries=true&autoReconnect=true
    username=ibcp_dev
    password=Ibcp_dev2017

  + 在工具类里面，使用静态代码块，读取属性

    ```java
    static{
        try {
            //1. 创建一个属性配置对象
            Properties properties = new Properties();
            InputStream is = new FileInputStream("jdbc.properties");//对应文件位于工程根目录
    
            //使用类加载器，去读取src底下的资源文件。 后面在servlet
            //InputStream is = JDBCUtil.class.getClassLoader().getResourceAsStream("jdbc.properties");//对应文件位于src目录下
            //导入输入流。
            properties.load(is);
    
            //读取属性，不要忘记双引号，不然报错空指针
            driverClass = properties.getProperty("driverClass");
            url = properties.getProperty("url");
            name = properties.getProperty("name");
            password = properties.getProperty("password");
    
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    ```

### database CRUD

+ insert

  `insert into goods values (null,'kd876',4,8,'ecs000000',1,1388.00,1665.60,9)`

+ delete

  `delete from goods where id = 6`

+ query

  `select * from goods;`

+ update

  `update goods set click_count = 200 where id=1; `

### use unit test to test code

+ 定义一个类，TestXXX，里面定义一个方法TestXXX。

+ 添加junit支持：右键工程 --- add Library --- Junit --- Junit4

+ 在方法的上面加上注解，其实就是一个标记

  ```java
  @Test
  public void testQuery(){
      ···
  }
  ```

+ 光标选中方法名字，然后右键执行单元测试。或者打开outline视图，然后选择方法右键执行。

### Dao Mode

+ Data Access Object 数据访问对象

  - 新建一个dao的接口，里面声明数据库访问规则。

    ```java
    public interface GoodsDao {
    	void findAll();
    }
    ```

  - 新建一个Dao的实现类implement，具体实现早前定义的规则。

    ```java
    public class GoodsDaoImpl implements GoodsDao {
    	@Override
    	public void findAll() {
    		Connection conn = null;
    		Statement st = null;
    		ResultSet rs = null;
    		try {
    			conn = JDBCUtil.getConn();
    			st = conn.createStatement();
    			String sql = "select * from goods";
    			rs = st.executeQuery(sql);
    			while(rs.next()) {
    				String name = rs.getString("goods_name");
    				int price = rs.getInt("shop_price");
    				int click = rs.getInt("click_count");
    				System.out.println("name=" + name + "===price=" + price + "==click=" + click);
    			}
    		} catch (Exception e) {
    			e.printStackTrace();
    		}finally {
    			JDBCUtil.release(conn, st, rs);
    		}
    	}
    }
    ```

  - 直接使用实现。

    ```java
    @Test
    public void testFindAll() {
        GoodsDao dao = new GoodsDaoImpl();
        dao.findAll();
    }
    ```

+ implement 接口，父类的引用指向子类的对象，接口的引用指向类的对象。

### Statement security issues

+ Statement执行，其实是拼接sql语句的。先拼接sql语句，然后再一起执行。容易出现安全问题。

  `' or '1=1'`

+ PreparedStatement，替换Statement对象。

  ```java
  Connection conn = null;
  Statement st = null;
  ResultSet rs = null;
  try {
      conn = JDBCUtil.getConn();
      st = conn.createStatement();
      String sql = "select * from goods where goods_name=? and goods_sn=?";
      PreparedStatement ps = conn.prepareStatement(sql);
      //占位符赋值
      ps.setString(1, username);
      ps.setString(2, password);
      rs = ps.executeQuery();
      if(rs.next()) {
          System.out.println("longin success");
      }else {
          System.out.println("longin fail");
      }
  } catch (Exception e) {
      e.printStackTrace();
  }finally {
      JDBCUtil.release(conn, st, rs);
  }
  ```

  - 相比较以前的statement，预先处理给定的sql语句，对其执行语法检查。在sql语句里面使用?占位符来替代后续要传递进来的变量。后面进来的变量值将会被看成是字符串，不会产生任何的关键字。

### Summary

+ JDBC入门
+ 抽取工具类
+ Statement CRUD
  - 演练CRUD

+ Dao模式
  - 声明与实现分开

+ PreparedStatement CRUD
  - 预处理sql语句，解决上面statement出现的问题

