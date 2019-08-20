# crud
增删改查工具:
查询单条记录、多条记录、查询数量、增删改

CRUDUtil类

package com.geshanzsq.crud;

import org.apache.log4j.Logger;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.*;

/**
 *
 * Notes：增删改查工具类
 * @author 格姗知识圈
 * @version 1.0
 * @date 2019/8/18 14:08
 */
public class CRUDUtil {

    private static Logger logger = Logger.getLogger(CRUDUtil.class);

    private static String driver;
    private static String url;
    private static String username;
    private static String password;

    static {
        init();
    }

    /**
     * Notes：初始化相关信息
     */
    private static void init() {
        InputStream is = CRUDUtil.class.getClassLoader().getResourceAsStream("jdbc.properties");
        Properties pro = new Properties();
        try {
            pro.load(is);
            driver = pro.getProperty("driverClassName");
            url = pro.getProperty("url");
            username = pro.getProperty("username");
            String password = pro.getProperty("password");
        } catch (IOException e) {
            logger.error(e.getMessage());
        } catch (Exception e) {
            logger.error(e.getMessage());
        }
    }

    /**
     * Notes：获得连接
     *
     * @return
     * @throws ClassNotFoundException
     * @throws SQLException
     */
    private static Connection getConnection() throws SQLException {
        try {
            Class.forName(driver);
        } catch (ClassNotFoundException e) {
            logger.error(e.getMessage());
        }
        Connection connection = DriverManager.getConnection(url, username, password);
        return connection;
    }

    /**
     * Notes：查询单条记录
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static Map<String, Object> querySimpleResult(String sql, List<Object> params) throws SQLException {
        Map<String, Object> resultMap = null;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.size(); i++) {
                    Object object = params.get(i);
                    ps.setObject(i + 1, object);
                }
            }
            rs = ps.executeQuery();
            ResultSetMetaData rsmd = rs.getMetaData();
            int col_count = rsmd.getColumnCount();//列数量
            while (rs.next()) {
                resultMap = new HashMap<String, Object>();
                for (int i = 0; i < col_count; i++) {
                    String colName = rsmd.getColumnName(i + 1);
                    Object colValue = rs.getObject(colName);
                    if (colValue == null && colValue instanceof String) {
                        colValue = "";
                    }
                    resultMap.put(colName, colValue);
                }
            }
        } finally {
            close(rs, ps, conn);
        }
        return resultMap;
    }

    /**
     * Notes：查询单条记录
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static Map<String, Object> querySimpleResult(String sql, Object... params) throws SQLException {
        Map<String, Object> resultMap = null;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.length; i++) {
                    ps.setObject(i + 1, params[i]);
                }
            }
            rs = ps.executeQuery();
            ResultSetMetaData rsmd = rs.getMetaData();
            int col_count = rsmd.getColumnCount();//列数量
            while (rs.next()) {
                resultMap = new HashMap<String, Object>();
                for (int i = 0; i < col_count; i++) {
                    String colName = rsmd.getColumnName(i + 1);
                    Object colValue = rs.getObject(colName);
                    if (colValue == null && colValue instanceof String) {
                        colValue = "";
                    }
                    resultMap.put(colName, colValue);
                }
            }
        } finally {
            close(rs, ps, conn);
        }
        return resultMap;
    }

    /**
     * Notes：查询多条记录
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static List<Map<String, Object>> queryMoreResult(String sql, List<Object> params) throws SQLException {
        List<Map<String, Object>> resultList = null;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.size(); i++) {
                    Object object = params.get(i);
                    ps.setObject(i + 1, object);
                }
            }
            rs = ps.executeQuery();
            ResultSetMetaData rsmd = rs.getMetaData();
            int col_count = rsmd.getColumnCount();//列数量
            resultList = new ArrayList<Map<String, Object>>();
            while (rs.next()) {
                Map<String, Object> map = new HashMap<String, Object>();
                for (int i = 0; i < col_count; i++) {
                    String colName = rsmd.getColumnName(i + 1);
                    Object colValue = rs.getObject(colName);
                    if (colValue == null && colValue instanceof String) {
                        colValue = "";
                    }
                    map.put(colName, colValue);
                }
                resultList.add(map);
            }
        } finally {
            close(rs, ps, conn);
        }
        return resultList;
    }

    /**
     * Notes：查询多条记录
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static List<Map<String, Object>> queryMoreResult(String sql, Object... params) throws SQLException {
        List<Map<String, Object>> resultList = null;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.length; i++) {
                    ps.setObject(i + 1, params[i]);
                }
            }
            rs = ps.executeQuery();
            ResultSetMetaData rsmd = rs.getMetaData();
            int col_count = rsmd.getColumnCount();//列数量
            resultList = new ArrayList<Map<String, Object>>();
            while (rs.next()) {
                Map<String, Object> map = new HashMap<String, Object>();
                for (int i = 0; i < col_count; i++) {
                    String colName = rsmd.getColumnName(i + 1);
                    Object colValue = rs.getObject(colName);
                    if (colValue == null && colValue instanceof String) {
                        colValue = "";
                    }
                    map.put(colName, colValue);
                }
                resultList.add(map);
            }
        } finally {
            close(rs, ps, conn);
        }
        return resultList;
    }

    /**
     * Notes：查询数量
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static int queryCount(String sql, List<Object> params) throws SQLException {
        int result = -1;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.size(); i++) {
                    Object object = params.get(i);
                    ps.setObject(i + 1, object);
                }
            }
            rs = ps.executeQuery();
            rs.next();
            result = rs.getInt(1);
        } finally {
            close(rs, ps, conn);
        }
        return result;
    }

    /**
     * Notes：查询数量
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static int queryCount(String sql, Object... params) throws SQLException {
        int result = -1;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.length; i++) {
                    ps.setObject(i + 1, params[i]);
                }
            }
            rs = ps.executeQuery();
            rs.next();
            result = rs.getInt(1);
        } finally {
            close(rs, ps, conn);
        }
        return result;
    }

    /**
     * Notes：增删改方法
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static int executeUpdate(String sql, List<Object> params) throws SQLException {
        int result = 0;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.size(); i++) {
                    Object object = params.get(i);
                    ps.setObject(i + 1, object);

                }
            }
            result = ps.executeUpdate();
        } finally {
            close(rs, ps, conn);
        }
        return result;
    }

    /**
     * Notes：增删改方法
     *
     * @param sql
     * @param params
     * @return
     * @throws SQLException
     * @throws ClassNotFoundException
     */
    public static int executeUpdate(String sql, Object... params) throws SQLException {
        int result = 0;
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            ps = conn.prepareStatement(sql);
            //设置参数
            if (params != null) {
                for (int i = 0; i < params.length; i++) {
                    ps.setObject(i + 1, params[i]);
                }
            }
            result = ps.executeUpdate();
        } finally {
            close(rs, ps, conn);
        }
        return result;
    }

    /**
     * Notes：关闭连接
     *
     * @param rs
     * @param ps
     * @param conn
     * @throws SQLException
     */
    public static void close(ResultSet rs, PreparedStatement ps, Connection conn) throws SQLException {
        if (rs != null) {
            rs.close();
        }
        if (ps != null) {
            ps.close();
        }
        if (conn != null) {
            conn.close();
        }
    }

    public static void main(String[] args) throws SQLException {

        //查询单条数据
        logger.info("------查询单条数据------");
        String sqlSimpleOne = "select id,name,sex from student where id=?";
        Map<String, Object> simpleMapOne = querySimpleResult(sqlSimpleOne, 1);
        logger.info("simpleMapOne:"+simpleMapOne);

        //查询单条数据
        String sqlSimpleTwo = "select id,name,sex from student where id=?";
        List<Object> paramList = new ArrayList<Object>();
        paramList.add(1);
        Map<String, Object> simpleMapTwo = querySimpleResult(sqlSimpleTwo, paramList);
        logger.info("simpleMapTwo:"+simpleMapTwo);

        //查询多条记录
        logger.info("------查询多条记录------");
        String sqlMoreOne = "select id,name,sex from student where sex=?";
        List<Map<String, Object>> moreListOne  = queryMoreResult(sqlMoreOne, "男");
        logger.info("moreListOne:"+moreListOne);

        String sqlMoreTwo = "select id,name,sex from student where sex=?";
        List<String> paramMoreList = new ArrayList<String>();
        paramList.add("男");
        List<Map<String, Object>> moreListTwo  = queryMoreResult(sqlMoreOne, paramList);
        logger.info("moreListTwo:"+moreListTwo);

    }

}
