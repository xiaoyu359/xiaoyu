## sphinx-serach引擎
### 安装配置
1. 下载路径：https://sphinxsearch.com/downloads/

  sphinx-search分为bin和src两种类型，bin类型下载下来后可以直接运行，src类型需要gcc编译。
2. 安装完成以后需要收到配置sphinx.conf文件，关于sphinx.conf的配置可详见-参考网址3，这里只提出一个重要的配置项
- searchd
listen是配置监控ip、端口、通信协议的，如果需要通过jdbc进行连接的话，需要配置协议为mysql41。
```
searchd
{
  ...
  listen               = 9306:mysql41
  mysql_version_string = 5.5.21
  ...
```
3. 启动方法
- 首先需要启用indexer，扫描sphinx.conf里面所有的index，将索引加载到文件中。

  可以通过indexer --all加载所有的索引
- 然后可以通过启动searchd，启动服务端，searchd也需要sphinx.conf配置文件支持。

  指定配置文件的启用方法是searchd --config sphinx.conf

### 通过java client连接sphinx

```
import java.sql.*;

public class JDBCSphinxQL {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("JDBC Driver not found!");
            e.printStackTrace();
            return;
        }
        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;
        try {
            System.out.println("start sphinx link!");
            conn = DriverManager
                    .getConnection("jdbc:mysql://127.0.0.1:9306?characterEncoding=utf8&maxAllowedPacket=512000", "", "");
            System.out.println("sphinx link success!");
        } catch (SQLException e) {
            System.out.println("Connection Failed! Check output console");
            e.printStackTrace();
            return;
        }
        try {
            conn.setReadOnly(true);
            st = conn.createStatement();
            System.out.println("Searching:");
            rs = st.executeQuery("SELECT * FROM testrt WHERE MATCH('some query')");
            while (rs.next()) {
                System.out.print(" id=" + rs.getString("id") + " gid="
                        + rs.getString("gid") + " latitude="
                        + rs.getString("latitude") + " longitude="
                        + rs.getString("longitude")
                );
                System.out.println();
            }
            // optional, to get total count and other stats
            System.out.println("Stats:");
            rs = st.executeQuery("SHOW META");
            while (rs.next()) {
                System.out.println(rs.getString(1) + ' ' + rs.getString(2));
            }
            // a simple insert
            st.executeUpdate("INSERT INTO testrt(id,title,content,gid,latitude,longitude)"
                    + " VALUES(104,'some title','some description',12345,32.1343,45.56534)");
            rs.close();
            st.close();
        } catch (SQLException e) {
            System.out.println("Queries failed!");
            e.printStackTrace();
            return;
        } finally {
            try {
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
                return;
            }
        }
    }
}
```


### 遇到的问题
1. sphinx-serach java client stucked

  解决方法：在searchd中添加mysql_version_string选项，不知道有没有必要，但是确实找到解决这个问题的突破口，最后发现是版本不匹配造成的。
使用的mysql是5.6的，但是使用的Sphinx是3.1.1的，最后下载了2.2.11-release调试通过了。

2. ClassCastException: java.math.BigInteger cannot be cast to java.lang.Long on connect to MySQL

  还是版本不匹配造成的，Sphinx 3.1.1对应的MySQL Connector/J的版本应该大于5，目前还不清楚对应哪个版本。

### 参考网址
1. https://www.ibm.com/developerworks/cn/opensource/os-sphinx/index.html
2. http://sphinxsearch.com/wiki/doku.php?id=sphinx_jdbc
3. http://sphinxsearch.com/docs/manual-2.3.2.html
