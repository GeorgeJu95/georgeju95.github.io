---
title: Java项目：一本糊涂账
date: 2020-03-09 20:02:36
categories: 
toc: true
tags: [Java]
---

本项目基于Swing和JDBC开发的图形界面桌面应用，涵盖了J2SE的绝大部分基础知识，通过这个项目能运用和锻炼几乎大部分的J2SE知识和技能。涉及到如下内容：

**基础内容：**面向对象 字符串数字 日期

**中级内容：**异常 ，集合，JDBC, 反射机制，I/O，Swing， 利用TableModel更新数据, 图形界面的皮肤

**高级内容：**图表chart动态生成，数据库的备份与恢复，自定义圆形进度条

**软件设计思想：**单例模式，面板类与监听器类松耦合，Entity层设计，DAO层设计，Service层设计

**业务常见处理手法：**CRUD操作，配置信息，配置信息初始化，报表生成，一对多关系，多对一关系

<!-- more -->

### 一、表结构设计

在专门的表结构设计章节里，会把每张表列出来，**每个字段的意义**，类型，**限制**。 表与表之间的关系，**一对多关系，多对一关系**如何确定与设计。**主键约束**，**外键约束**等等信息。

```sql
create database hutubill;
 
use  hutubill;
  
CREATE TABLE config (
  id int AUTO_INCREMENT,
  key_ varchar(255) ,
  value varchar(255) ,
  PRIMARY KEY (id)
)  DEFAULT CHARSET=utf8;
  
CREATE TABLE category (
  id int AUTO_INCREMENT,
  name varchar(255) ,
  PRIMARY KEY (id)
)  DEFAULT CHARSET=utf8;
  
CREATE TABLE record (
  id int AUTO_INCREMENT,
  spend int,
  cid int,
  comment varchar(255) ,
  date Date,
  PRIMARY KEY (id),
  CONSTRAINT `fk_record_category` FOREIGN KEY (`cid`) REFERENCES `category` (`id`)
)  DEFAULT CHARSET=utf8;
```

### 二、原型设计

什么是原型设计呢？ 简单说，就是先把界面做出来。 界面上的数据，都是假数据，并不是从数据库中读取的真实数据。

为什么要这么做呢？ 有了界面，才直观，你才会更有感觉，也才能更清楚各个功能之间怎么实现。 更重要的是，有了界面，才能**更有效的和客户沟通**，哪些功能需要修改，哪些功能可以删减。

原型这个模块涵盖**包的设计与规划**，**工具类的抽象**，自定义界面类，各种面板类等等。

* **单例模式**。比如SpendPanel，只需要一个实例就可以了，**不需要每次显示这个面板的时候都创建新的实例**。 不仅是不需要，**也不应该**每一次都创建新的实例。

  * **饿汉式**是立即加载的方式，无论是否会用到这个对象，都会加载。
    如果在构造方法里写了性能消耗较大，占时较久的代码，比如建立与数据库的连接，那么就会在启动的时候感觉稍微有些卡顿。

    **懒汉式**，是延迟加载的方式，只有使用的时候才会加载。 并且有[线程安全](https://how2j.cn/k/thread/thread-synchronized/355.html#step793)的考量。
    使用懒汉式，在启动的时候，会感觉到比饿汉式略快，因为并没有做对象的实例化。 但是在第一次调用的时候，会进行实例化操作，感觉上就略慢。

    看业务需求，如果业务上允许有比较充分的启动和初始化时间，就使用饿汉式，否则就使用懒汉式

  * 单例模式三元素
    * 构造方法私有化
    * 静态属性指向实例
    * public static 的 getInstance方法，返回上面的静态属性

```java
饿汉式单例模式
package charactor;
 
public class GiantDragon {
 
    //私有化构造方法使得该类无法在外部通过new 进行实例化
    private GiantDragon(){
         
    }
 
    //准备一个类属性，指向一个实例化对象。 因为是类属性，所以只有一个
 
    private static GiantDragon instance = new GiantDragon();
     
    //public static 方法，提供给调用者获取12行定义的对象
    public static GiantDragon getInstance(){
        return instance;
    }
     
}

懒汉式单例模式
package charactor;
 
public class GiantDragon {
  
    //私有化构造方法使得该类无法在外部通过new 进行实例化
    private GiantDragon(){       
    }
  
    //准备一个类属性，用于指向一个实例化对象，但是暂时指向null
    private static GiantDragon instance;
      
    //public static 方法，返回实例对象
    public static GiantDragon getInstance(){
        //第一次访问的时候，发现instance没有指向任何对象，这时实例化一个对象
        if(null==instance){
            instance = new GiantDragon();
        }
        //返回 instance指向的对象
        return instance;
    }     
}
```

* 自定义一些工具类，一些常用的功能全添加到工具包中，以后需要使用就直接调用

比如Swing并没有提供一种可以很简单就居中的布局器，但是这样的布局器又很常见，所以在这里就自己开发一个专门用于居中的面板。

首先设计一个类，继承了JPanel，有**三个属性**

```java
private double rate;//拉伸比例
private JComponent c; //显示的组件
private boolean strech; //是否拉伸
```

**show(JComponent c)**

使用show方法显示组件，show方法中的思路是：先把这个容器中的组件都移出，然后把新的组件加进来，并且调用updateUI进行界面渲染。updateUI会导致swing去调用repaint()方法。

在repaint方法中，就会使用绝对定位的方式把组件放在中间位置。如果strech是true，就会根据整个容器的大小，设置组件的大小，达到拉伸的效果；如果strech是false, 就使用组件的preferredSize，即非拉伸效果。

**构造方法 public CenterPanel(double rate, boolean strech)**

rate 表示拉伸比例，1就是填满，0.5就是填一半。strech 表示是否拉伸

```java
package util;
  
import java.awt.Component;
 
import java.awt.Dimension;
 
import javax.swing.JButton;
import javax.swing.JComponent;
import javax.swing.JFrame;
import javax.swing.JPanel;
  
public class CenterPanel extends JPanel {
  
    private double rate;//拉伸比例
    private JComponent c; //显示的组件
    private boolean strech; //是否拉伸
  
    public CenterPanel(double rate,boolean strech) {
        this.setLayout(null);
        this.rate = rate;
        this.strech = strech;
    }
      
    public CenterPanel(double rate) {
        this(rate,true);
    }  
  
    public void repaint() {
        if (null != c) {
            Dimension containerSize = this.getSize();
            Dimension componentSize= c.getPreferredSize();
              
            if(strech)
                c.setSize((int) (containerSize.width * rate), (int) (containerSize.height * rate));
            else
                c.setSize(componentSize);
  
            c.setLocation(containerSize.width / 2 - c.getSize().width / 2, containerSize.height / 2 - c.getSize().height / 2);
        }
        super.repaint();
    }
  
    public void show(JComponent p) {
        this.c = p;
        Component[] cs = getComponents();
        for (Component c : cs) {
            remove(c);
        }
        add(p);
        this.updateUI();
    }
      
    public static void main(String[] args) {
        JFrame f = new JFrame();
        f.setSize(200, 200);
        f.setLocationRelativeTo(null);
        CenterPanel cp = new CenterPanel(0.85,true);
        f.setContentPane(cp);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        f.setVisible(true);
        JButton b = new JButton("abc");
        cp.show(b);
    }
}
```

### 三、实体类与DAO的设计

在开始功能开发之前，首先要设计实体类与DAO。相关的数据库操作，都经由DAO来完成。

实体类Entity 有很多种其他的说法，比如Bean,JavaBean, pojo等等，其实都是相近的东西。

实体类Entity 是**用于映射表中的一条一条数据**的。比如分类表Category，有很多条分类记录，那么就有一个类也叫做Category，这个类的一个一个的实例，就对应了表Category中的一条一条的记录。

ConfigDAO专门用于把Config实例与Config表进行ORM映射。
**ORM映射**是什么呢？ 简单点理解就是ConfigDAO负责把Config实例转换成一条Config表中的记录，反过来，又把Config表中的记录转换为一个Config实例。

```java
package dao;
 
import java.sql.Connection;
 
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
 
import entity.Config;
import util.DBUtil;
 
public class ConfigDAO {
 
    public int getTotal() {
        int total = 0;
        try (Connection c = DBUtil.getConnection(); Statement s = c.createStatement();) {
 
            String sql = "select count(*) from config";
 
            ResultSet rs = s.executeQuery(sql);
            while (rs.next()) {
                total = rs.getInt(1);
            }
 
            System.out.println("total:" + total);
 
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
        return total;
    }
 
    public void add(Config config) {
 
        String sql = "insert into config values(null,?,?)";
        try (Connection c = DBUtil.getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
            ps.setString(1, config.key);
            ps.setString(2, config.value);
            ps.execute();
            ResultSet rs = ps.getGeneratedKeys();
            if (rs.next()) {
                int id = rs.getInt(1);
                config.id = id;
            }
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
    }
 
    public void update(Config config) {
 
        String sql = "update config set key_= ?, value=? where id = ?";
        try (Connection c = DBUtil.getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
 
            ps.setString(1, config.key);
            ps.setString(2, config.value);
            ps.setInt(3, config.id);
 
            ps.execute();
 
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
 
    }
 
    public void delete(int id) {
 
        try (Connection c = DBUtil.getConnection(); Statement s = c.createStatement();) {
 
            String sql = "delete from config where id = " + id;
 
            s.execute(sql);
 
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
    }
 
    public Config get(int id) {
        Config config = null;
 
        try (Connection c = DBUtil.getConnection(); Statement s = c.createStatement();) {
 
            String sql = "select * from config where id = " + id;
 
            ResultSet rs = s.executeQuery(sql);
 
            if (rs.next()) {
                config = new Config();
                String key = rs.getString("key_");
                String value = rs.getString("value");
                config.key = key;
                config.value = value;
                config.id = id;
            }
 
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
        return config;
    }
 
    public List<Config> list() {
        return list(0, Short.MAX_VALUE);
    }
 
    public List<Config> list(int start, int count) {
        List<Config> configs = new ArrayList<Config>();
 
        String sql = "select * from config order by id desc limit ?,? ";
 
        try (Connection c = DBUtil.getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
 
            ps.setInt(1, start);
            ps.setInt(2, count);
 
            ResultSet rs = ps.executeQuery();
 
            while (rs.next()) {
                Config config = new Config();
                int id = rs.getInt(1);
                String key = rs.getString("key_");
                String value = rs.getString("value");
                config.id = id;
                config.key = key;
                config.value = value;
                configs.add(config);
            }
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
        return configs;
    }
 
    public Config getByKey(String key) {
        Config config = null;
        String sql = "select * from config where key_ = ?" ;
        try (Connection c = DBUtil.getConnection(); 
                PreparedStatement ps = c.prepareStatement(sql);
            ) {
             
            ps.setString(1, key);
            ResultSet rs =ps.executeQuery();
 
            if (rs.next()) {
                config = new Config();
                int id = rs.getInt("id");
                String value = rs.getString("value");
                config.key = key;
                config.value = value;
                config.id = id;
            }
 
        } catch (SQLException e) {
 
            e.printStackTrace();
        }
        return config;
    }
}
```

### 四、功能开发

启动类**Bootstrap**

```java
package startup;
 
import javax.swing.SwingUtilities;
 
import gui.frame.MainFrame;
import gui.panel.MainPanel;
import gui.panel.SpendPanel;
import util.GUIUtil;
 
public class Bootstrap {
    public static void main(String[] args) throws Exception{
        GUIUtil.useLNF();
 
        SwingUtilities.invokeAndWait(new Runnable() {
            @Override
            public void run() {
                MainFrame.instance.setVisible(true);
                MainPanel.instance.workingPanel.show(SpendPanel.instance);
            }
        });
    }
}
```

首先是确定**多层结构**，基于事件驱动，规划DAO层，Service层。 并且在开发过程中，演示重构并解释重构的理由和好处。

接着是**开发顺序**。 各个模块之间是互相依赖的，有的需要先行开发，有的模块必须建立在其他模块的基础上才可以执行。

1. 主窗体工具栏事件响应 (主窗体工具栏中的按钮，点击之后，切换不同的面板)

   ```java
   /* 
   设计一个独立的监听器类ToolBarListener ，实现接口ActionListener ，重写actionPerformed方法。
   这个监听器是为工具栏上的几个按钮添加的，并且这几个按钮都使用这么一个监听器。
   通过ActionEvent.getSource()获取事件是哪个按钮发出来的，根据不同的按钮，发出切换不同的功能面板。
   */
   
   package gui.listener;
    
   import java.awt.event.ActionEvent;
   import java.awt.event.ActionListener;
    
   import javax.swing.JButton;
    
   import gui.panel.BackupPanel;
   import gui.panel.CategoryPanel;
   import gui.panel.ConfigPanel;
   import gui.panel.SpendPanel;
   import gui.panel.MainPanel;
   import gui.panel.RecordPanel;
   import gui.panel.RecoverPanel;
   import gui.panel.ReportPanel;
    
   public class ToolBarListener implements ActionListener {
       @Override
       public void actionPerformed(ActionEvent e) {
           MainPanel p = MainPanel.instance;
           JButton b = (JButton) e.getSource();
           if (b == p.bReport)
               p.workingPanel.show(ReportPanel.instance);
           if (b == p.bCategory)
               p.workingPanel.show(CategoryPanel.instance);
           if (b == p.bSpend)
               p.workingPanel.show(SpendPanel.instance);
           if (b == p.bRecord)
               p.workingPanel.show(RecordPanel.instance);
           if (b == p.bConfig)
               p.workingPanel.show(ConfigPanel.instance);
           if (b == p.bBackup)
               p.workingPanel.show(BackupPanel.instance);
           if (b == p.bRecover)
               p.workingPanel.show(RecoverPanel.instance);
    
       }
   }
   
   
   
   /*
   实例化一个ToolBarListener 监听器，工具栏上的按钮，都加上这么一个监听器对象即可。
   */
   
   package gui.panel;
    
   import java.awt.BorderLayout;
    
   import javax.swing.JButton;
   import javax.swing.JPanel;
   import javax.swing.JToolBar;
    
   import gui.listener.ToolBarListener;
   import util.CenterPanel;
   import util.GUIUtil;
    
   public class MainPanel extends JPanel {
       static {
           GUIUtil.useLNF();
       }
    
       public static MainPanel instance = new MainPanel();
       public JToolBar tb = new JToolBar();
       public JButton bSpend = new JButton();
       public JButton bRecord = new JButton();
       public JButton bCategory = new JButton();
       public JButton bReport = new JButton();
       public JButton bConfig = new JButton();
       public JButton bBackup = new JButton();
       public JButton bRecover = new JButton();
    
       public CenterPanel workingPanel;
    
       private MainPanel() {
    
           GUIUtil.setImageIcon(bSpend, "home.png", "消费一览");
           GUIUtil.setImageIcon(bRecord, "record.png", "记一笔");
           GUIUtil.setImageIcon(bCategory, "category2.png", "消费分类");
           GUIUtil.setImageIcon(bReport, "report.png", "月消费报表");
           GUIUtil.setImageIcon(bConfig, "config.png", "设置");
           GUIUtil.setImageIcon(bBackup, "backup.png", "备份");
           GUIUtil.setImageIcon(bRecover, "restore.png", "恢复");
    
           tb.add(bSpend);
           tb.add(bRecord);
           tb.add(bCategory);
           tb.add(bReport);
           tb.add(bConfig);
           tb.add(bBackup);
           tb.add(bRecover);
           tb.setFloatable(false);
    
           workingPanel = new CenterPanel(0.8);
    
           setLayout(new BorderLayout());
           add(tb, BorderLayout.NORTH);
           add(workingPanel, BorderLayout.CENTER);
            
           addListener();
       }
    
       private void addListener() {
           ToolBarListener listener = new ToolBarListener();
            
           bSpend.addActionListener(listener);
           bRecord.addActionListener(listener);
           bCategory.addActionListener(listener);
           bReport.addActionListener(listener);
           bConfig.addActionListener(listener);
           bBackup.addActionListener(listener);
           bRecover.addActionListener(listener);
            
       }
    
       public static void main(String[] args) {
           GUIUtil.showPanel(MainPanel.instance, 1);
       }
   }
   ```

2. 配置

   1. 业务类Service

      如图所示，由监听器调用Service业务类，然后再调用DAO访问数据库。

      ![Service](/assets/blogImg/service.png)

      ```java
      /*
      ConfigService 设置业务类，这个类是监听器直接调用的类，然后再通过ConfigService去调用ConfigDAO。
      为什么需要一个业务类呢？ DAO是直接和数据库打交道的，在和数据库打交道之前，还需要对数据进行预处理，这些就可以放在业务类里进行。
      */
      package service;
       
      import dao.ConfigDAO;
      import entity.Config;
       
      public class ConfigService {
          public static final String budget = "budget";
          public static final String mysqlPath = "mysqlPath";
          public static final String default_budget = "500";
       
          static ConfigDAO dao= new ConfigDAO();
          static{
              init();
          }
       
          public static void init(){
              init(budget, default_budget);
              init(mysqlPath, "");
          }
           
          private static void init(String key, String value) {
               
              Config config= dao.getByKey(key);
              if(config==null){
                  Config c = new Config();
                  c.setKey(key);
                  c.setValue(value);
                  dao.add(c);
              }
          }
       
          public String get(String key) {
              Config config= dao.getByKey(key);
              return config.getValue();
          }
           
          public void update(String key, String value){
              Config config= dao.getByKey(key);
              config.setValue(value);
              dao.update(config);    
          }
           
          public int getIntBudget() {
              return Integer.parseInt(get(budget));
          }    
      }
      ```

3. 消费分类

   ```java
   /*
   业务类
   */
   package service;
    
   import java.util.Collections;
   import java.util.List;
    
   import dao.CategoryDAO;
   import dao.RecordDAO;
   import entity.Category;
   import entity.Record;
    
   public class CategoryService {
    
       CategoryDAO categoryDao = new CategoryDAO();
       RecordDAO recordDao = new RecordDAO();
    
       public List<Category> list() {
           List<Category> cs= categoryDao.list();
           for (Category c : cs) {
               List<Record> rs =recordDao.list(c.id);
               c.recordNumber=rs.size();
           }
           Collections.sort(cs,(c1,c2)->c2.recordNumber-c1.recordNumber);
            
           return cs;
       }
    
       public void add(String name) {
           Category c = new Category();
           c.setName(name);
           categoryDao.add(c);
       }
    
       public void update(int id, String name) {
           Category c = new Category();
           c.setName(name);
           c.setId(id);
           categoryDao.update(c);
       }
    
       public void delete(int id) {
           categoryDao.delete(id);
       }
   }
   
   
   /*
   显示Table信息用到了CategoryTableModel，与记一笔面板RecordPanel原型中的CategoryComboBoxModel一样.
   TableModel的设计思想：在Model这种思想的指导下，数据（TableModel）和显示（JTable渲染）分离开来了。 比如对于JTable而言，有数据部分，也有显示部分(比如列宽等信息)。 数据部分，专门做一个类，叫做TableModel，就用于存放要显示的数据。
   */
   package gui.model;
     
   import java.util.List;
    
   import javax.swing.table.AbstractTableModel;
    
   import entity.Category;
   import service.CategoryService;
     
   public class CategoryTableModel extends AbstractTableModel {
     
       String[] columnNames = new String[] { "分类名称", "消费次数"};
     
       // 使用从Service返回的List作为TableModel的数据
     
       public List<Category> cs = new CategoryService().list();
     
        public int getRowCount() {
            
           return cs.size();
       }
     
       public int getColumnCount() {
            
           return columnNames.length;
       }
     
       public String getColumnName(int columnIndex) {
            
           return columnNames[columnIndex];
       }
     
       public boolean isCellEditable(int rowIndex, int columnIndex) {
           return false;
       }
     
       // 先通过cs.get(rowIndex)获取行对应的Category对象
       // 然后根据columnIndex返回对应的属性
       public Object getValueAt(int rowIndex, int columnIndex) {
           Category h = cs.get(rowIndex);
           if (0 == columnIndex)
               return h.name;
           if (1 == columnIndex)
               return h.recordNumber;
    
           return null;
       } 
   }
   
   
   /*
   1. 为CategoryPanel新增加一个getSelectedCategory，方便获取JTable上当前选中的Category对象。
   2. 增加一个updateData方法，用于在增加，删除，和修改之后，更新表格中的信息，并默认选中第一行。除此之外，还进行判断，如果表格里没有数据，删除和修改按钮不可使用。
   */
   package gui.panel;
    
   import java.awt.BorderLayout;
    
   import javax.swing.JButton;
   import javax.swing.JPanel;
   import javax.swing.JScrollPane;
   import javax.swing.JTable;
    
   import entity.Category;
   import gui.model.CategoryTableModel;
   import service.CategoryService;
   import util.ColorUtil;
   import util.GUIUtil;
    
   public class CategoryPanel extends JPanel {
       static {
           GUIUtil.useLNF();
       }
    
       public static CategoryPanel instance = new CategoryPanel();
    
       public JButton bAdd = new JButton("新增");
       public JButton bEdit = new JButton("编辑");
       public JButton bDelete = new JButton("删除");
       String columNames[] = new String[] { "分类名称", "消费次数" };
    
       public CategoryTableModel ctm = new CategoryTableModel();
       public JTable t = new JTable(ctm);
    
       public CategoryPanel() {
           GUIUtil.setColor(ColorUtil.blueColor, bAdd, bEdit, bDelete);
           JScrollPane sp = new JScrollPane(t);
           JPanel pSubmit = new JPanel();
           pSubmit.add(bAdd);
           pSubmit.add(bEdit);
           pSubmit.add(bDelete);
    
           this.setLayout(new BorderLayout());
           this.add(sp, BorderLayout.CENTER);
           this.add(pSubmit, BorderLayout.SOUTH);
       }
    
       public static void main(String[] args) {
           GUIUtil.showPanel(CategoryPanel.instance);
       }
    
       public Category getSelectedCategory() {
           int index = t.getSelectedRow();
           return ctm.cs.get(index);
       }
        
       public void updateData() {
           ctm.cs = new CategoryService().list();
           t.updateUI();
           t.getSelectionModel().setSelectionInterval(0, 0);
            
           if(0==ctm.cs.size()){
               bEdit.setEnabled(false);
               bDelete.setEnabled(false);
           }
           else{
               bEdit.setEnabled(true);
               bDelete.setEnabled(true);
           }
       }  
   }
   
   
   /*
   CategoryListener监听器，增加，编辑和删除按钮都使用这个监听器。
   */
   package gui.listener;
    
   import java.awt.event.ActionEvent;
   import java.awt.event.ActionListener;
    
   import javax.swing.JButton;
   import javax.swing.JOptionPane;
    
   import entity.Category;
   import gui.panel.CategoryPanel;
   import service.CategoryService;
    
   public class CategoryListener implements ActionListener {
    
       @Override
       public void actionPerformed(ActionEvent e) {
           CategoryPanel p = CategoryPanel.instance;
    
           JButton b = (JButton) e.getSource();
           if (b == p.bAdd) {
               String name = JOptionPane.showInputDialog(null);
               if (0 == name.length()) {
                   JOptionPane.showMessageDialog(p, "分类名称不能为空");
                   return;
               }
    
               new CategoryService().add(name);
    
           }
           if (b == p.bEdit) {
               Category c = p.getSelectedCategory();
               int id = c.id;
               String name = JOptionPane.showInputDialog("修改分类名称", c.name);
               if (0 == name.length()) {
                   JOptionPane.showMessageDialog(p, "分类名称不能为空");
                   return;
               }
    
               new CategoryService().update(id, name);
           }
           if (b == p.bDelete) {
               Category c = p.getSelectedCategory();
               if (0 != c.recordNumber) {
                   JOptionPane.showMessageDialog(p, "本分类下有消费记录存在，不能删除");
                   return;
               }
               if (JOptionPane.OK_OPTION != JOptionPane.showConfirmDialog(p, "确认要删除？"))
                   return;
    
               int id = c.id;
               new CategoryService().delete(id);
           }
           p.updateData();
       }
   }
   ```

4. **代码演化和重构**，各种面板类实现WorkingPanel接口、监听器的完善，改进CenterPanel

   ```java
   /*
   功能开发到这个阶段，可以总结出一些规律出来
   1. 在设置面板ConfigPanel和分类面板CategoryPanel里都有addListener接口
   2. 面板在显示的时候，都需要从数据库中读取信息，并显示在界面上，比如CategoryPanel的updateData方法。 ConfigPanel虽然暂时没有updateData这么一个方法，但是也是有这个需要的(当点击工具栏上的设置按钮的时候，也是需要把预算信息读取出来，并显示在面板上的)
   
   可以预见到，后面的各种面板类，都有类似的功能需求。 这样，就可以抽象出一个WorkingPanel类，来提供这样的功能
   */
   package gui.panel;
    
   import javax.swing.JPanel;
    
   public abstract class WorkingPanel  extends JPanel{
       public abstract void updateData();
       public abstract void addListener();
   }
   
   
   /*
   监听器ToolBarListener 目前做的工作是把面板显示出来。 但是并不能保证面板上刷新数据库中的信息，比如点击设置后，虽然能够切换到预算面板，但是看到的预算值依然是0。如何改进呢？
   
   留意监听器ToolBarListener 中的这段代码  p.workingPanel.show(ConfigPanel.instance);
   workingPanel是CenterPanel类型的，那么只需要在其show方法中判断，如果是WorkingPanel类型，那么就可以调用其updateData来做到把数据更新到界面上了。这样就达到了这样的效果：点击工具栏上的按钮，不仅可以切换到不同的面板，而且面板上的数据也马上从数据库中更新。
   */
   package util;
    
   import java.awt.Component;
   import java.awt.Dimension;
    
   import javax.swing.JButton;
   import javax.swing.JComponent;
   import javax.swing.JFrame;
   import javax.swing.JPanel;
    
   import gui.panel.WorkingPanel;
    
   public class CenterPanel extends JPanel {
    
       private double rate;// 拉伸比例
       private JComponent c; // 显示的组件
       private boolean strech; // 是否拉伸
    
       public CenterPanel(double rate, boolean strech) {
           this.setLayout(null);
           this.rate = rate;
           this.strech = strech;
       }
    
       public CenterPanel(double rate) {
           this(rate, true);
       }
    
       public void repaint() {
           if (null != c) {
               Dimension containerSize = this.getSize();
               Dimension componentSize = c.getPreferredSize();
    
               if (strech)
                   c.setSize((int) (containerSize.width * rate), (int) (containerSize.height * rate));
               else
                   c.setSize(componentSize);
    
               c.setLocation(containerSize.width / 2 - c.getSize().width / 2,
                       containerSize.height / 2 - c.getSize().height / 2);
           }
           super.repaint();
       }
    
       public void show(JComponent p) {
           this.c = p;
           Component[] cs = getComponents();
           for (Component c : cs) {
               remove(c);
           }
           add(p);
    
           if (p instanceof WorkingPanel)
               ((WorkingPanel) p).updateData();
    
           this.updateUI();
       }
    
       public static void main(String[] args) {
           JFrame f = new JFrame();
           f.setSize(200, 200);
           f.setLocationRelativeTo(null);
           CenterPanel cp = new CenterPanel(0.85, true);
           f.setContentPane(cp);
           f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
           f.setVisible(true);
           JButton b = new JButton("abc");
           cp.show(b);
    
       }
   }
   ```

5. 记一笔

   ```java
   /*
   业务类
   */
   package service;
    
   import java.util.Date;
    
   import dao.RecordDAO;
   import entity.Category;
   import entity.Record;
    
   public class RecordService {
       RecordDAO recordDao = new RecordDAO();
       public void add(int spend, Category c, String comment,Date date){
           Record r = new Record();
           r.spend = spend;
           r.cid = c.id;
           r.comment = comment;
           r.date = date;
           recordDao.add(r);
       }
   }
   
   
   /*
   CategoryComboBoxModel
   */
   package gui.model;
    
   import java.util.List;
    
   import javax.swing.ComboBoxModel;
   import javax.swing.event.ListDataListener;
    
   import entity.Category;
   import service.CategoryService;
    
   public class CategoryComboBoxModel implements ComboBoxModel<Category>{
    
       public List<Category> cs = new CategoryService().list();
        
       public Category c;
        
       public CategoryComboBoxModel(){
           if(!cs.isEmpty())
               c=cs.get(0);
       }
        
       @Override
       public int getSize() {
           // TODO Auto-generated method stub
            
           return cs.size();
       }
    
       @Override
       public Category getElementAt(int index) {
           // TODO Auto-generated method stub
           return cs.get(index);
       }
    
       @Override
       public void addListDataListener(ListDataListener l) {
           // TODO Auto-generated method stub
            
       }
    
       @Override
       public void removeListDataListener(ListDataListener l) {
           // TODO Auto-generated method stub
            
       }
    
       @Override
       public void setSelectedItem(Object anItem) {
           c = (Category) anItem;
       }
    
       @Override
       public Object getSelectedItem() {
           if(!cs.isEmpty())
               return c;
           else
               return null;
       }
   }
   
   
   /*
   给界面上的"记一笔“ 按钮添加监听，没有分类信息提示先增加消费分类，添加成功后，切换到消费一览
   */
   package gui.listener;
    
   import java.awt.event.ActionEvent;
   import java.awt.event.ActionListener;
   import java.util.Date;
    
   import javax.swing.JOptionPane;
    
   import entity.Category;
   import gui.panel.CategoryPanel;
   import gui.panel.MainPanel;
   import gui.panel.RecordPanel;
   import gui.panel.SpendPanel;
   import service.RecordService;
   import util.GUIUtil;
    
   public class RecordListener implements ActionListener {
    
       @Override
       public void actionPerformed(ActionEvent e) {
           RecordPanel p  =RecordPanel.instance;
           if(0==p.cbModel.cs.size()){
               JOptionPane.showMessageDialog(p, "暂无消费分类，无法添加，请先增加消费分类");
               MainPanel.instance.workingPanel.show(CategoryPanel.instance);
               return;
           }
            
           if(!GUIUtil.checkZero(p.tfSpend,"花费金额"))
               return;
           int spend = Integer.parseInt(p.tfSpend.getText());
           Category c = p.getSelectedCategory();
           String comment = p.tfComment.getText();
           Date d = p.datepick.getDate();
           new RecordService().add(spend, c, comment, d);
           JOptionPane.showMessageDialog(p, "添加成功");
            
           MainPanel.instance.workingPanel.show(SpendPanel.instance);        
       }
   }
   
   
   /*
   RecordPanel
   */
   package gui.panel;
    
   import java.awt.BorderLayout;
   import java.awt.GridLayout;
   import java.util.Date;
    
   import javax.swing.JButton;
   import javax.swing.JComboBox;
   import javax.swing.JLabel;
   import javax.swing.JPanel;
   import javax.swing.JTextField;
    
   import org.jdesktop.swingx.JXDatePicker;
    
   import entity.Category;
   import gui.listener.RecordListener;
   import gui.model.CategoryComboBoxModel;
   import service.CategoryService;
   import util.ColorUtil;
   import util.GUIUtil;
    
   public class RecordPanel extends WorkingPanel {
       static{
           GUIUtil.useLNF();
       }
       public static RecordPanel instance = new RecordPanel();
    
       JLabel lSpend = new JLabel("花费(￥)");
       JLabel lCategory = new JLabel("分类");
       JLabel lComment = new JLabel("备注");
       JLabel lDate = new JLabel("日期");
    
       public JTextField tfSpend = new JTextField("0");
    
       public CategoryComboBoxModel cbModel = new CategoryComboBoxModel();
       public JComboBox<Category> cbCategory = new JComboBox<>(cbModel);
       public JTextField tfComment = new JTextField();
       public JXDatePicker datepick = new JXDatePicker(new Date());
        
       JButton bSubmit = new JButton("记一笔");
    
       public RecordPanel() {
           GUIUtil.setColor(ColorUtil.grayColor, lSpend,lCategory,lComment,lDate);
           GUIUtil.setColor(ColorUtil.blueColor, bSubmit);
           JPanel pInput =new JPanel();
           JPanel pSubmit = new JPanel();
           int gap = 40;
           pInput.setLayout(new GridLayout(4,2,gap,gap));
            
           pInput.add(lSpend);
           pInput.add(tfSpend);
           pInput.add(lCategory);
           pInput.add(cbCategory);
           pInput.add(lComment);
           pInput.add(tfComment);
           pInput.add(lDate);
           pInput.add(datepick);
            
           pSubmit.add(bSubmit);
            
           this.setLayout(new BorderLayout());
           this.add(pInput,BorderLayout.NORTH);
           this.add(pSubmit,BorderLayout.CENTER);
            
           addListener();
       }
    
       public static void main(String[] args) {
           GUIUtil.showPanel(RecordPanel.instance);
       }
    
       public Category getSelectedCategory(){
           return (Category) cbCategory.getSelectedItem();
       }
    
       @Override
       public void updateData() {
           cbModel.cs = new CategoryService().list();
           cbCategory.updateUI();
           resetInput();
           tfSpend.grabFocus();
       }
        
       public void resetInput(){
           tfSpend.setText("0");
           tfComment.setText("");
           if(0!=cbModel.cs.size())
               cbCategory.setSelectedIndex(0);
           datepick.setDate(new Date());
       }  
    
       @Override
       public void addListener() {
           // TODO Auto-generated method stub
           RecordListener listener = new RecordListener();
           bSubmit.addActionListener(listener);
       }
   }
   ```

6. 消费一览

   ```java
   /*
   SpendPage 专门用于SpendPanel 消费一览面板 的页面类
   */
   package gui.page;
    
   public class SpendPage {
       //本月消费
       public String monthSpend;
       //今日消费
       public String todaySpend;
       //日均消费
       public String avgSpendPerDay;
       //本月剩余
       public String monthAvailable;
       //日均可用
       public String dayAvgAvailable;
       //距离月末
       public String monthLeftDay;
       //使用比例
       public int usagePercentage;
       //是否超支
       public boolean isOverSpend = false;
    
       public SpendPage(int monthSpend, int todaySpend, int avgSpendPerDay, int monthAvailable, int dayAvgAvailable,
               int monthLeftDay, int usagePercentage) {
           this.monthSpend = "￥" + monthSpend;
           this.todaySpend = "￥" + todaySpend;
           this.avgSpendPerDay = "￥" + avgSpendPerDay;
           if (monthAvailable < 0)
               isOverSpend = true;
    
           if (!isOverSpend) {
               this.monthAvailable = "￥" + monthAvailable;
               this.dayAvgAvailable = "￥" + dayAvgAvailable;
           } else {
               this.monthAvailable = "超支" + (0 - monthAvailable);
               this.dayAvgAvailable = "￥0";
           }
    
           this.monthLeftDay = monthLeftDay + "天";
           this.usagePercentage = usagePercentage;
       }
   }
   
   
   /*
   业务类，SpendService消费一览业务类的getSpendPage()方法，返回SpendPage 用于界面显示。
   */
   package service;
    
   import java.util.List;
    
   import dao.RecordDAO;
   import entity.Record;
   import gui.page.SpendPage;
   import util.DateUtil;
    
   public class SpendService {
    
       public SpendPage getSpendPage() {
           RecordDAO dao = new RecordDAO();
           // 本月数据
           List<Record> thisMonthRecords = dao.listThisMonth();
           // 今日数据
           List<Record> toDayRecords = dao.listToday();
           // 本月总天数
           int thisMonthTotalDay = DateUtil.thisMonthTotalDay();
    
           int monthSpend = 0;
           int todaySpend = 0;
           int avgSpendPerDay = 0;
           int monthAvailable = 0;
           int dayAvgAvailable = 0;
           int monthLeftDay = 0;
           int usagePercentage = 0;
    
           // 预算
           int monthBudget = new ConfigService().getIntBudget();
    
           // 统计本月消费
           for (Record record : thisMonthRecords) {
               monthSpend += record.getSpend();
           }
    
           // 统计今日消费
           for (Record record : toDayRecords) {
               todaySpend += record.getSpend();
           }
           // 计算日均消费
           avgSpendPerDay = monthSpend / thisMonthTotalDay;
           // 计算本月剩余
           monthAvailable = monthBudget - monthSpend;
    
           // 距离月末
           monthLeftDay = DateUtil.thisMonthLeftDay();
    
           // 计算日均可用
           dayAvgAvailable = monthAvailable / monthLeftDay;
    
           // 计算使用比例
           usagePercentage = monthSpend * 100 / monthBudget;
    
           // 根据这些信息，生成SpendPage对象
    
           return new SpendPage(monthSpend, todaySpend, avgSpendPerDay, monthAvailable, dayAvgAvailable, monthLeftDay,
                   usagePercentage);
       }
   }
   
   
   /*
   SpendPanel
   */
   package gui.panel;
    
   import static util.GUIUtil.setColor;
   import static util.GUIUtil.showPanel;
    
   import java.awt.BorderLayout;
   import java.awt.Color;
   import java.awt.Component;
   import java.awt.Font;
   import java.awt.GridLayout;
    
   import javax.swing.JLabel;
   import javax.swing.JPanel;
    
   import gui.page.SpendPage;
   import service.SpendService;
   import util.CircleProgressBar;
   import util.ColorUtil;
    
   public class SpendPanel extends WorkingPanel {
       public static SpendPanel instance = new SpendPanel();
    
       JLabel lMonthSpend = new JLabel("本月消费");
       JLabel lTodaySpend = new JLabel("今日消费");
       JLabel lAvgSpendPerDay = new JLabel("日均消费");
       JLabel lMonthLeft = new JLabel("本月剩余");
       JLabel lDayAvgAvailable = new JLabel("日均可用");
       JLabel lMonthLeftDay = new JLabel("距离月末");
    
       JLabel vMonthSpend = new JLabel("￥2300");
       JLabel vTodaySpend = new JLabel("￥25");
       JLabel vAvgSpendPerDay = new JLabel("￥120");
       JLabel vMonthAvailable = new JLabel("￥2084");
       JLabel vDayAvgAvailable = new JLabel("￥389");
       JLabel vMonthLeftDay = new JLabel("15天");
    
       CircleProgressBar bar;
    
       public SpendPanel() {
           this.setLayout(new BorderLayout());
           bar = new CircleProgressBar();
           bar.setBackgroundColor(ColorUtil.blueColor);
    
           setColor(ColorUtil.grayColor, lMonthSpend, lTodaySpend, lAvgSpendPerDay, lMonthLeft, lDayAvgAvailable,
                   lMonthLeftDay, vAvgSpendPerDay, vMonthAvailable, vDayAvgAvailable, vMonthLeftDay);
           setColor(ColorUtil.blueColor, vMonthSpend, vTodaySpend);
    
           vMonthSpend.setFont(new Font("微软雅黑", Font.BOLD, 23));
           vTodaySpend.setFont(new Font("微软雅黑", Font.BOLD, 23));
    
           this.add(center(), BorderLayout.CENTER);
           this.add(south(), BorderLayout.SOUTH);
    
       }
    
       private JPanel center() {
           JPanel p = new JPanel();
           p.setLayout(new BorderLayout());
           p.add(west(), BorderLayout.WEST);
           p.add(east());
    
           return p;
       }
    
       private Component east() {
    
           return bar;
       }
    
       private Component west() {
           JPanel p = new JPanel();
           p.setLayout(new GridLayout(4, 1));
           p.add(lMonthSpend);
           p.add(vMonthSpend);
           p.add(lTodaySpend);
           p.add(vTodaySpend);
           return p;
       }
    
       private JPanel south() {
           JPanel p = new JPanel();
           p.setLayout(new GridLayout(2, 4));
    
           p.add(lAvgSpendPerDay);
           p.add(lMonthLeft);
           p.add(lDayAvgAvailable);
           p.add(lMonthLeftDay);
           p.add(vAvgSpendPerDay);
           p.add(vMonthAvailable);
           p.add(vDayAvgAvailable);
           p.add(vMonthLeftDay);
    
           return p;
       }
    
       public static void main(String[] args) {
           showPanel(SpendPanel.instance);
       }
    
       @Override
       public void updateData() {
           SpendPage spend = new SpendService().getSpendPage();
           vMonthSpend.setText(spend.monthSpend);
           vTodaySpend.setText(spend.todaySpend);
           vAvgSpendPerDay.setText(spend.avgSpendPerDay);
           vMonthAvailable.setText(spend.monthAvailable);
           vDayAvgAvailable.setText(spend.dayAvgAvailable);
           vMonthLeftDay.setText(spend.monthLeftDay);
    
           bar.setProgress(spend.usagePercentage);
           if (spend.isOverSpend) {
               vMonthAvailable.setForeground(ColorUtil.warningColor);
               vMonthSpend.setForeground(ColorUtil.warningColor);
               vTodaySpend.setForeground(ColorUtil.warningColor);
    
           } else {
               vMonthAvailable.setForeground(ColorUtil.grayColor);
               vMonthSpend.setForeground(ColorUtil.blueColor);
               vTodaySpend.setForeground(ColorUtil.blueColor);
           }
           bar.setForegroundColor(ColorUtil.getByPercentage(spend.usagePercentage));
           addListener();
    
       }
    
       @Override
       public void addListener() {
           // TODO Auto-generated method stub
       }
   }
   ```

7. 月消费报表

   ```java
   /*
   业务类
   */
   package service;
    
   import java.text.SimpleDateFormat;
   import java.util.ArrayList;
   import java.util.Calendar;
   import java.util.Date;
   import java.util.List;
    
   import dao.RecordDAO;
   import entity.Record;
   import util.DateUtil;
    
   public class ReportService {
       /*
        *下面的是两个修改版
        *用这种方法才能通过if判断得出daySpend
        *日期转成一样格式的String字符串去对比才能通过if的equals比对
        */
       public int getDaySpend(Date d, List<Record> monthRawDate){
           int daySpend = 0;
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
           String nowDate = sdf.format(d);
           for(Record record : monthRawDate){
               String date = sdf.format(record.date);
               if(date.equals(nowDate))
                   daySpend += record.spend;
           }
           System.out.println("此天支出为"+daySpend);
           return daySpend;
       }
   
   
       /*
        *即使用DateUtil去转换，也是不能通过if对比，导致daySpend一直为0
        */
   //    public int getDaySpend(Date d, List<Record> monthRawDate){
   //        int daySpend = 0;
   //        for(Record record : monthRawDate){
   //            if(DateUtil.util2sql(record.date).equals(DateUtil.util2sql(d))) {
   //                System.out.println(d); // 打印不出来，说明根本没通过if语句
   //                daySpend += record.spend;
   //            }
   //        }
   //        System.out.println("此天收入为"+daySpend);
   //        return daySpend;
   //    }
   
   
   
   
       /**
        * 获取某一天的消费金额
        * @param d
        * @param monthRawData
        * @return
        */
   //    public int getDaySpend(Date d,List<Record> monthRawData){
   //        int daySpend = 0;
   //        for (Record record : monthRawData) {
   //            if(record.date.equals(d))
   //                daySpend+=record.spend;
   //        }
   //        return daySpend;
   //    }
   
       /**
        * 获取一个月的消费记录集合
        * @return
        */
       public List<Record> listThisMonthRecords() {
           RecordDAO dao= new RecordDAO();
           List<Record> monthRawData= dao.listThisMonth();
           List<Record> result= new ArrayList<>();
           Date monthBegin = DateUtil.monthBegin();
           int monthTotalDay = DateUtil.thisMonthTotalDay();
           Calendar c = Calendar.getInstance();
           for (int i = 0; i < monthTotalDay; i++) {
               Record r = new Record();
               c.setTime(monthBegin);
               c.add(Calendar.DATE, i);
               Date eachDayOfThisMonth=c.getTime() ;
               System.out.printf("第%d次调用getDaySpend，也就是本月第%d天 %n", i+1, i+1);
               int daySpend = getDaySpend(eachDayOfThisMonth,monthRawData);
               r.spend=daySpend;
               result.add(r);
           }
           return result;
       }
   }
   
   
   
   /*
   图表类
   */
   package util;
    
   import java.awt.Color;
   import java.awt.Font;
   import java.awt.Image;
   import java.util.List;
    
   import javax.swing.Icon;
   import javax.swing.ImageIcon;
   import javax.swing.JLabel;
   import javax.swing.JPanel;
    
   import com.objectplanet.chart.BarChart;
   import com.objectplanet.chart.Chart;
    
   import entity.Record;
    
   public class ChartUtil {
       private static String[] sampleLabels(List<Record> rs) {
           String[] sampleLabels = new String[rs.size()];
           for (int i = 0; i < sampleLabels.length; i++) {
               if (0 == i % 5)
                   sampleLabels[i] = String.valueOf(i + 1 + "日");
           }
    
           return sampleLabels;
    
       }
    
       public static double[] sampleValues(List<Record> rs) {
           double[] sampleValues = new double[rs.size()];
           for (int i = 0; i < sampleValues.length; i++) {
               sampleValues[i] = rs.get(i).spend;
           }
    
           return sampleValues;
       }
    
       public static Image getImage(List<Record> rs, int width, int height) {
           // 根据消费记录得到的样本数据
           double[] sampleValues = sampleValues(rs);
           // 根据消费记录得到的下方日期文本
           String[] sampleLabels = sampleLabels(rs);
           // 样本中的最大值
           int max = max(sampleValues);
    
           // 数据颜色
           Color[] sampleColors = new Color[] { ColorUtil.blueColor };
    
           // 柱状图
           BarChart chart = new BarChart();
    
           // 设置样本个数
           chart.setSampleCount(sampleValues.length);
           // 设置样本数据
           chart.setSampleValues(0, sampleValues);
           // 设置文字
           chart.setSampleLabels(sampleLabels);
           // 设置样本颜色
           chart.setSampleColors(sampleColors);
           // 设置取值范围
           chart.setRange(0, max * 1.2);
           // 显示背景横线
           chart.setValueLinesOn(true);
           // 显示文字
           chart.setSampleLabelsOn(true);
           // 把文字显示在下方
           chart.setSampleLabelStyle(Chart.BELOW);
    
           // 样本值的字体
           chart.setFont("rangeLabelFont", new Font("Arial", Font.BOLD, 12));
           // 显示图例说明
           chart.setLegendOn(true);
           // 把图例说明放在左侧
           chart.setLegendPosition(Chart.LEFT);
           // 图例说明中的文字
           chart.setLegendLabels(new String[] { "月消费报表" });
           // 图例说明的字体
           chart.setFont("legendFont", new Font("Dialog", Font.BOLD, 13));
           // 下方文字的字体
           chart.setFont("sampleLabelFont", new Font("Dialog", Font.BOLD, 13));
           // 图表中间背景颜色
           chart.setChartBackground(Color.white);
           // 图表整体背景颜色
           chart.setBackground(ColorUtil.backgroundColor);
           // 把图表转换为Image类型
           Image im = chart.getImage(width, height);
           return im;
       }
    
       public static int max(double[] sampleValues) {
           int max = 0;
           for (double v : sampleValues) {
               if (v > max)
                   max = (int) v;
           }
           return max;
    
       }
    
     
       // 此处是测试原型数据，上面的才是涉及数据库的代码  
       private static String[] sampleLabels() {
           String[] sampleLabels = new String[30];
    
           for (int i = 0; i < sampleLabels.length; i++) {
               if (0 == i % 5)
                   sampleLabels[i] = String.valueOf(i + 1 + "日");
           }
           return sampleLabels;
       }
    
       public static Image getImage(int width, int height) {
           // 模拟样本数据
           double[] sampleValues = sampleValues();
           // 下方显示的文字
           String[] sampleLabels = sampleLabels();
           // 样本中的最大值
           int max = max(sampleValues);
    
           // 数据颜色
           Color[] sampleColors = new Color[] { ColorUtil.blueColor };
    
           // 柱状图
           BarChart chart = new BarChart();
    
           // 设置样本个数
           chart.setSampleCount(sampleValues.length);
           // 设置样本数据
           chart.setSampleValues(0, sampleValues);
           // 设置文字
           chart.setSampleLabels(sampleLabels);
           // 设置样本颜色
           chart.setSampleColors(sampleColors);
           // 设置取值范围
           chart.setRange(0, max * 1.2);
           // 显示背景横线
           chart.setValueLinesOn(true);
           // 显示文字
           chart.setSampleLabelsOn(true);
           // 把文字显示在下方
           chart.setSampleLabelStyle(Chart.BELOW);
    
           // 样本值的字体
           chart.setFont("rangeLabelFont", new Font("Arial", Font.BOLD, 12));
           // 显示图例说明
           chart.setLegendOn(true);
           // 把图例说明放在左侧
           chart.setLegendPosition(Chart.LEFT);
           // 图例说明中的文字
           chart.setLegendLabels(new String[] { "月消费报表" });
           // 图例说明的字体
           chart.setFont("legendFont", new Font("Dialog", Font.BOLD, 13));
           // 下方文字的字体
           chart.setFont("sampleLabelFont", new Font("Dialog", Font.BOLD, 13));
           // 图表中间背景颜色
           chart.setChartBackground(Color.white);
           // 图表整体背景颜色
           chart.setBackground(ColorUtil.backgroundColor);
           // 把图表转换为Image类型
           Image im = chart.getImage(width, height);
           return im;
       }
    
       private static double[] sampleValues() {
    
           double[] result = new double[30];
           for (int i = 0; i < result.length; i++) {
               result[i] = (int) (Math.random() * 300);
           }
           return result;
    
       }
    
       public static void main(String[] args) {
           JPanel p = new JPanel();
           JLabel l = new JLabel();
           Image img = ChartUtil.getImage(400, 300);
           Icon icon = new ImageIcon(img);
           l.setIcon(icon);
           p.add(l);
           GUIUtil.showPanel(p);
       }
   }
   
   
   
   /*
   面板类
   */
   package gui.panel;
    
   import static util.GUIUtil.showPanel;
    
   import java.awt.BorderLayout;
   import java.awt.Image;
   import java.util.List;
    
   import javax.swing.ImageIcon;
   import javax.swing.JLabel;
    
   import entity.Record;
   import service.ReportService;
   import util.ChartUtil;
    
   public class ReportPanel extends WorkingPanel {
       public static ReportPanel instance = new ReportPanel();
    
       JLabel l = new JLabel();
    
       public ReportPanel() {
           this.setLayout(new BorderLayout());
           List<Record> rs = new ReportService().listThisMonthRecords();
           Image i = ChartUtil.getImage(rs, 400, 300);
           ImageIcon icon = new ImageIcon(i);
           l.setIcon(icon);
           this.add(l);
           addListener();
       }
    
       public static void main(String[] args) {
           showPanel(ReportPanel.instance);
       }
    
       @Override
       public void updateData() {
           List<Record> rs = new ReportService().listThisMonthRecords();
           Image i = ChartUtil.getImage(rs, 350, 250);
           ImageIcon icon = new ImageIcon(i);
           l.setIcon(icon);
       }
    
       @Override
       public void addListener() {
    
       }
   }
   ```

8. 备份

   接下来是备份和恢复功能，在开始这两个功能之前 ，先设计一个 Mysql专门的工具类 MysqlUtil

   ```java
   /*
   备份方法backup()
   通过Runtime调用mysqldump.exe进行备份
   
   恢复方法recover
   通过Runtime调用mysql.exe进行数据的还原
   */
   package util;
    
   import java.io.BufferedReader;
    
   import java.io.FileInputStream;
   import java.io.IOException;
   import java.io.InputStreamReader;
   import java.io.OutputStream;
   import java.io.OutputStreamWriter;
    
   public class MysqlUtil {
    
       public static void backup(String mysqlPath, String backupfile) throws IOException {
           //注意MAC下执行，下面应为 "%s/bin/mysqldump -u%s -p%s   -hlocalhost   -P%d %s -r %s"
           String commandFormat = "\"%s/bin/mysqldump.exe\" -u%s -p%s   -hlocalhost   -P%d %s -r \"%s\"";
    
           String command = String.format(commandFormat, mysqlPath, DBUtil.loginName, DBUtil.password, DBUtil.port,
                   DBUtil.database, backupfile);
           Runtime.getRuntime().exec(command);
       }
    
       public static void recover(String mysqlPath, String recoverfile) {
           try {
               String commandFormat = "\"%s/bin/mysql.exe\" -u%s -p%s   %s ";
               String command = String.format(commandFormat, mysqlPath, DBUtil.loginName, DBUtil.password,
                       DBUtil.database);
    
               Process p = Runtime.getRuntime().exec(command);
               OutputStream out = p.getOutputStream();
               String inStr;
               StringBuffer sb = new StringBuffer("");
               String outStr;
               BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(recoverfile), "utf8"));
               while ((inStr = br.readLine()) != null) {
                   // 注意 MAC 系统 需要写成"\n"
                   sb.append(inStr + "\r\n");
               }
               outStr = sb.toString();
    
               OutputStreamWriter writer = new OutputStreamWriter(out, "utf8");
               writer.write(outStr);
               writer.flush();
               out.close();
               br.close();
               writer.close();
           } catch (Exception e) {
               e.printStackTrace();
           }
    
       }
    
       public static void main(String[] args) throws IOException {
           String mysqlPath = "D:/tools/MYSQL/mysql-5.1.57-win32";
           String file = "C:/Documents and Settings/Administrator/My Documents/hutubill.sql";
    
           // backup(mysqlPath, file);
           // restore();
           // recover(mysqlPath, file);
           // recover(file);
           recover(mysqlPath, file);
    
       }
   }
   
   
   /*
   备份面板
   */
   package gui.panel;
    
   import javax.swing.JButton;
    
   import gui.listener.BackupListener;
   import util.ColorUtil;
   import util.GUIUtil;
    
   public class BackupPanel extends WorkingPanel {
       static {
           GUIUtil.useLNF();
       }
    
       public static BackupPanel instance = new BackupPanel();
       JButton bBackup = new JButton("备份");
    
       public BackupPanel() {
           GUIUtil.setColor(ColorUtil.blueColor, bBackup);
           this.add(bBackup);
           addListener();
       }
    
       public static void main(String[] args) {
           GUIUtil.showPanel(BackupPanel.instance);
       }
    
       @Override
       public void updateData() {
    
       }
    
       @Override
       public void addListener() {
           BackupListener listener = new BackupListener();
           bBackup.addActionListener(listener);
       }
   }
   
   
   /*
   监听器
   */
   package gui.listener;
    
   import java.awt.event.ActionEvent;
   import java.awt.event.ActionListener;
   import java.io.File;
   import java.io.IOException;
    
   import javax.swing.JFileChooser;
   import javax.swing.JOptionPane;
   import javax.swing.filechooser.FileFilter;
    
   import gui.panel.BackupPanel;
   import gui.panel.ConfigPanel;
   import gui.panel.MainPanel;
   import service.ConfigService;
   import util.MysqlUtil;
    
   public class BackupListener implements ActionListener {
    
       @Override
       public void actionPerformed(ActionEvent e) {
           BackupPanel p  =BackupPanel.instance;
           String mysqlPath= new ConfigService().get(ConfigService.mysqlPath);
           if(0==mysqlPath.length()){
               JOptionPane.showMessageDialog(p, "备份前请事先配置mysql的路径");
               MainPanel.instance.workingPanel.show(ConfigPanel.instance);
               ConfigPanel.instance.tfMysqlPath.grabFocus();
               return;
           }
           JFileChooser fc = new JFileChooser();
           fc.setSelectedFile(new File("hutubill.sql"));
           fc.setFileFilter(new FileFilter() {
                
               @Override
               public String getDescription() {
                   return ".sql";
               }
                
               @Override
               public boolean accept(File f) {
                   return f.getName().toLowerCase().endsWith(".sql");
               }
           });
    
            int returnVal =  fc.showSaveDialog(p);
            File file = fc.getSelectedFile();
            System.out.println(file);
            if (returnVal == JFileChooser.APPROVE_OPTION) {
                //如果保存的文件名没有以.sql结尾，自动加上.sql
                System.out.println(file);
                if(!file.getName().toLowerCase().endsWith(".sql"))
                    file = new File(file.getParent(),file.getName()+".sql");
                System.out.println(file);
                 
               try {
                   MysqlUtil.backup(mysqlPath, file.getAbsolutePath());
                   JOptionPane.showMessageDialog(p, "备份成功,备份文件位于:\r\n"+file.getAbsolutePath());
               } catch (Exception e1) {
                   e1.printStackTrace();
                   JOptionPane.showMessageDialog(p, "备份失败\r\n,错误:\r\n"+e1.getMessage());   
               }       
            }      
       }
   }
   ```

9. 恢复

   ```java
   package gui.listener;
    
   import java.awt.event.ActionEvent;
   import java.awt.event.ActionListener;
   import java.io.File;
   import java.io.IOException;
    
   import javax.swing.JFileChooser;
   import javax.swing.JOptionPane;
   import javax.swing.filechooser.FileFilter;
    
   import gui.panel.BackupPanel;
   import gui.panel.ConfigPanel;
   import gui.panel.MainPanel;
   import service.ConfigService;
   import util.MysqlUtil;
    
   public class RecoverListener implements ActionListener {
    
       @Override
       public void actionPerformed(ActionEvent e) {
           BackupPanel p  =BackupPanel.instance;
           String mysqlPath= new ConfigService().get(ConfigService.mysqlPath);
           if(0==mysqlPath.length()){
               JOptionPane.showMessageDialog(p, "恢复前请事先配置mysql的路径");
               MainPanel.instance.workingPanel.show(ConfigPanel.instance);
               ConfigPanel.instance.tfMysqlPath.grabFocus();
               return;
           }
           JFileChooser fc = new JFileChooser();
           fc.setSelectedFile(new File("hutubill.sql"));
           fc.setFileFilter(new FileFilter() {
                
               @Override
               public String getDescription() {
                   return ".sql";
               }
                
               @Override
               public boolean accept(File f) {
                   return f.getName().toLowerCase().endsWith(".sql");
               }
           });
    
            int returnVal =  fc.showOpenDialog(p);
            File file = fc.getSelectedFile();
            System.out.println(file);
            if (returnVal == JFileChooser.APPROVE_OPTION) {
               try {
                   MysqlUtil.recover(mysqlPath,file.getAbsolutePath());
   //              MysqlUtil.recover(mysqlPath, file.getAbsolutePath());
                   JOptionPane.showMessageDialog(p, "恢复成功");
               } catch (Exception e1) {
                   e1.printStackTrace();
                   JOptionPane.showMessageDialog(p, "恢复失败\r\n,错误:\r\n"+e1.getMessage());   
               }
                 
            }      
       }
   }
   ```

最后，不要忘了**引用第三方的包**。 比如动态生成chart图片，演示如何使用这些API。

### 五、改进与总结

在原有基础上添加查看消费记录**详情**和**编辑**的功能

* 详情相关

```java
/*
消费详情，面板类
*/
package gui.panel;

import entity.Category;
import entity.Record;
import gui.listener.DetailListener;
import gui.model.CategoryComboBoxModel;
import gui.model.DetailTableModel;
import service.CategoryService;
import service.DetailService;
import service.RecordService;
import util.ColorUtil;
import util.GUIUtil;

import javax.swing.*;
import java.awt.*;
import java.util.Collections;
import java.util.Comparator;

public class DetailPanel extends WorkingPanel{
    static {
        GUIUtil.useLNF();
    }
    public static DetailPanel instance = new DetailPanel();

    public JButton bEdit = new JButton("编辑");
    public JButton bDelete = new JButton("删除");
    public JButton bFilter = new JButton("筛选");
    public JButton bAll = new JButton("查看全部");
    public boolean updataFlag = false;
    public DetailService dServer = new DetailService();

    public DetailTableModel dtm = new DetailTableModel();
    public JTable t = new JTable(dtm);

    public CategoryComboBoxModel cbModel = new CategoryComboBoxModel();
    public JComboBox<Category> cbCategory = new JComboBox<>(cbModel);
    public JLabel lCategory = new JLabel("查看分类");

    public DetailPanel() {
        GUIUtil.setColor(ColorUtil.grayColor, lCategory);
        GUIUtil.setColor(ColorUtil.blueColor, bEdit, bDelete);

        this.setLayout(new BorderLayout());
        this.add(south(), BorderLayout.SOUTH);
        this.add(center(), BorderLayout.CENTER);

        addListener();
    }

    private JPanel center() {
        JPanel pInput = new JPanel();
        pInput.setLayout(new BorderLayout());
        JScrollPane sp = new JScrollPane(t);
        t.getColumnModel().getColumn(0).setPreferredWidth(10);
        t.getColumnModel().getColumn(2).setPreferredWidth(30);

        pInput.add(sp, BorderLayout.CENTER);
        pInput.add(north(), BorderLayout.NORTH);

        return pInput;
    }

    private Component north() {
        JPanel pInput = new JPanel();
        pInput.setLayout(new FlowLayout());
        cbCategory.setPreferredSize(new Dimension(100, 20));
        bAll.setPreferredSize(new Dimension(80, 20));
        bFilter.setPreferredSize(new Dimension(80, 20));

        pInput.add(lCategory);
        pInput.add(cbCategory);
        pInput.add(bFilter);
        pInput.add(bAll);
        return pInput;
    }

    private JPanel south() {
        JPanel pSubmit = new JPanel();
        pSubmit.add(bEdit);
        pSubmit.add(bDelete);

        return pSubmit;
    }

    public static void main(String[] args) {
        GUIUtil.showPanel(DetailPanel.instance);
    }

    public Category getSelectedCategory(){
        return (Category) cbCategory.getSelectedItem();
    }

    public Record getSelectedRecord() {
        int index = t.getSelectedRow();
        return dtm.ds.get(index);
    }

    @Override
    public void updateData() {
        if (updataFlag) {
            dtm.ds = dServer.list(getSelectedCategory().id);
        } else {
            dtm.ds = dServer.list();
        }
//        按时间顺序排序消费记录
        dtm.ds.sort((r1, r2) -> r1.date.getTime() <= r2.date.getTime() ? 1 : -1);

        t.updateUI();

        if (dtm.ds.size() == 0) {
            bEdit.setEnabled(false);
            bDelete.setEnabled(false);
        } else {
            bEdit.setEnabled(true);
            bDelete.setEnabled(true);
        }

        cbModel.cs = new CategoryService().list();
        cbModel.cs.sort(Comparator.comparing(c -> c.id));
        cbCategory.updateUI();
        if(0!=cbModel.cs.size())
            cbCategory.setSelectedIndex(0);
    }

    @Override
    public void addListener() {
        DetailListener listener = new DetailListener();
        bFilter.addActionListener(listener);
        bAll.addActionListener(listener);
        bEdit.addActionListener(listener);
        bDelete.addActionListener(listener);
    }
}



/*
DetailTableModel类
*/
package gui.model;

import dao.CategoryDAO;
import dao.RecordDAO;
import entity.Category;
import entity.Record;
import service.DetailService;

import javax.swing.event.TableModelListener;
import javax.swing.table.AbstractTableModel;
import javax.swing.table.TableModel;
import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class DetailTableModel extends AbstractTableModel {

    String[] columNames = new String[]{"分类", "日期", "金额", "备注"};
    DetailService dServer = new DetailService();
    public List<Record> ds = dServer.list();

    @Override
    public int getRowCount() {
        return ds.size();
    }

    @Override
    public int getColumnCount() {
        return columNames.length;
    }

    @Override
    public String getColumnName(int columnIndex) {
        return columNames[columnIndex];
    }

    @Override
    public boolean isCellEditable(int rowIndex, int columnIndex) {
        return false;
    }

    @Override
    public Object getValueAt(int rowIndex, int columnIndex) {
        Record r = ds.get(rowIndex);
        if (columnIndex == 0) {
            return dServer.getCategoryName(r);
        }
        if (columnIndex == 1) {
            return r.date;
        }
        if (columnIndex == 2) {
            return r.spend;
        }
        if (columnIndex == 3) {
            return r.comment;
        }

        return null;
    }

    @Override
    public void setValueAt(Object o, int i, int i1) {

    }

    @Override
    public void addTableModelListener(TableModelListener tableModelListener) {

    }

    @Override
    public void removeTableModelListener(TableModelListener tableModelListener) {

    }
}




/*
消费记录详情 业务类
*/
package service;

import dao.CategoryDAO;
import dao.RecordDAO;
import entity.Category;
import entity.Record;
import gui.panel.DetailPanel;

import java.util.Date;
import java.util.List;

public class DetailService {
    CategoryDAO categoryDao = new CategoryDAO();
    RecordDAO recordDao = new RecordDAO();

    public List<Record> list() {
        return recordDao.list();
    }

    public List<Record> list(int cid) {
        return recordDao.list(cid);
    }

    public String getCategoryName(Record r) {
        return categoryDao.get(r.cid).name;
    }

    public void update(int spend, int id, int cid, String comment, Date date) {
        Record r = new Record();
        r.setSpend(spend);
        r.setId(id);
        r.setCid(cid);
        r.setComment(comment);
        r.setDate(date);
        recordDao.update(r);
    }

    public void delete(int id) {
        recordDao.delete(id);
    }
}



/*
消费详情监听器
*/
package gui.listener;

import entity.Record;
import gui.frame.EditFrame;
import gui.panel.DetailPanel;
import gui.panel.EditPanel;
import service.DetailService;

import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class DetailListener implements ActionListener {

    @Override
    public void actionPerformed(ActionEvent e) {
        DetailPanel d = DetailPanel.instance;
        JButton b = (JButton) e.getSource();
        EditFrame fEdit = EditFrame.instance;
        EditPanel ep = EditPanel.instance;

        if (b == d.bEdit) {
            ep.updateData();
            fEdit.setVisible(true);
            fEdit.workingPanel.show(EditPanel.instance);
        }
        if (b == d.bAll) {
            d.updataFlag = false;
        }
        if (b == d.bFilter) {
            d.updataFlag = true;
        }
        if (b == d.bDelete) {
            Record r = d.getSelectedRecord();

            if (JOptionPane.OK_OPTION != JOptionPane.showConfirmDialog(d, "确认要删除？"))
                return;

            int id = r.id;
            new DetailService().delete(id);
        }
        d.updateData();
    }
}
```

* 编辑相关

```java
/*
编辑 面板类
*/
package gui.panel;

import entity.Category;
import gui.listener.EditListener;
import gui.model.CategoryComboBoxModel;
import org.jdesktop.swingx.JXDatePicker;
import service.CategoryService;
import util.ColorUtil;
import util.GUIUtil;

import javax.swing.*;
import java.awt.*;
import java.util.Comparator;
import java.util.Date;

public class EditPanel extends JPanel{
    static{
        GUIUtil.useLNF();
    }
    public static EditPanel instance = new EditPanel();

    JLabel lSpend = new JLabel("花费(￥)");
    JLabel lCategory = new JLabel("分类");
    JLabel lComment = new JLabel("备注");
    JLabel lDate = new JLabel("日期");

    public JTextField tfSpend = new JTextField("0");
    public JTextField tfComment = new JTextField("");

    public CategoryComboBoxModel cbModel = new CategoryComboBoxModel();
    public JComboBox<Category> cbCategory = new JComboBox<>(cbModel);

    public JXDatePicker datepick = new JXDatePicker(new Date());

    public JButton bDetermine = new JButton("确定");
    public JButton bCancel = new JButton("取消");

    public EditPanel() {
        GUIUtil.setColor(ColorUtil.grayColor, lSpend,lCategory,lComment,lDate);
        GUIUtil.setColor(ColorUtil.blueColor, bDetermine, bCancel);
        JPanel pInput =new JPanel();
        JPanel pSubmit = new JPanel();
        int gap = 25;
        pInput.setLayout(new GridLayout(4, 2, gap, gap));

        pInput.add(lSpend);
        pInput.add(tfSpend);
        pInput.add(lCategory);
        pInput.add(cbCategory);
        pInput.add(lComment);
        pInput.add(tfComment);
        pInput.add(lDate);
        pInput.add(datepick);

        pSubmit.add(bDetermine);
        pSubmit.add(bCancel);

        this.setLayout(new BorderLayout());
        this.add(pInput,BorderLayout.NORTH);
        this.add(pSubmit,BorderLayout.CENTER);

        addListener();
    }

    public Category getSelectedCategory(){
        return (Category) cbCategory.getSelectedItem();
    }

    public static void main(String[] args) {
        GUIUtil.showPanel(EditPanel.instance);
    }

    public void updateData() {
        cbModel.cs = new CategoryService().list();
        cbModel.cs.sort(Comparator.comparing(c -> c.id));
        cbCategory.updateUI();
        resetInput();
        tfSpend.grabFocus();
    }

    public void resetInput(){
        tfSpend.setText(String.valueOf(DetailPanel.instance.getSelectedRecord().spend));
        tfComment.setText(DetailPanel.instance.getSelectedRecord().comment);
        if(0!=cbModel.cs.size())
            cbCategory.setSelectedIndex(DetailPanel.instance.getSelectedRecord().cid - 1);
        datepick.setDate(DetailPanel.instance.getSelectedRecord().date);
    }

    public void addListener() {
        EditListener listener = new EditListener();
        bCancel.addActionListener(listener);
        bDetermine.addActionListener(listener);
    }
}


/*
编辑 主框体
*/
package gui.frame;

import gui.panel.EditPanel;
import gui.panel.MainPanel;
import util.CenterPanel;

import javax.swing.*;

public class EditFrame extends JFrame {
    public static EditFrame instance = new EditFrame();
    public CenterPanel workingPanel;

    EditFrame() {
        workingPanel = new CenterPanel(0.8);

        this.setSize(500, 300);
        this.setTitle("编辑");
        this.setContentPane(workingPanel);
        this.setLocationRelativeTo(null);
        this.setResizable(false);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }

    public static void main(String[] args) {
        EditFrame instance = new EditFrame();
        instance.setVisible(true);
        instance.workingPanel.show(EditPanel.instance);
    }
}




/*
编辑 监听器
*/
package gui.listener;

import entity.Category;
import gui.frame.EditFrame;
import gui.panel.CategoryPanel;
import gui.panel.DetailPanel;
import gui.panel.EditPanel;
import gui.panel.MainPanel;
import service.RecordService;
import util.GUIUtil;

import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.Date;

public class EditListener implements ActionListener {
    @Override
    public void actionPerformed(ActionEvent e) {
        DetailPanel dp = DetailPanel.instance;
        EditPanel ep = EditPanel.instance;
        JButton b = (JButton) e.getSource();

        if (b == ep.bDetermine) {
            if(0==ep.cbModel.cs.size()){
                JOptionPane.showMessageDialog(ep, "暂无消费分类，无法添加，请先增加消费分类");
                MainPanel.instance.workingPanel.show(CategoryPanel.instance);
                return;
            }

            if(!GUIUtil.checkZero(ep.tfSpend,"花费金额"))
                return;
            int spend = Integer.parseInt(ep.tfSpend.getText());
            Category c = ep.getSelectedCategory();
            String comment = ep.tfComment.getText();
            Date d = ep.datepick.getDate();
            int id = dp.getSelectedRecord().id;
            new RecordService().update(spend, c, comment, d, id);
            JOptionPane.showMessageDialog(ep, "修改成功");
            EditFrame.instance.setVisible(false);

            dp.updateData();
        }
        if (b == ep.bCancel) {
            EditFrame.instance.setVisible(false);
        }
    }
}
```

* 其他细节

```java
// RecordService 增加一个更新方法
public void update(int spend, Category c, String comment, Date date, int id) {
        Record r = new Record();
        r.id = id;
        r.spend = spend;
        r.cid = c.id;
        r.comment = comment;
        r.date = date;
        recordDao.update(r);
}


// 主界面面板，有一些地方要修改
public JButton bDetail = new JButton();

GUIUtil.setImageIcon(bDetail, "report.png", "消费详情");

tb.add(bDetail);

bDetail.addActionListener(listener);


// ToolBarListener
if (b == p.bReport)
  p.workingPanel.show(ReportPanel.instance);
```

#### 整体思路
创建一个`DetailPanel`面板用来显示消费详情，在编辑键上添加一个新的`EditFrame`用来编辑选定的记录
#### 布局
该面板布局分为上中下三个部分，上部放置筛选按钮和分类选择框，中部放置表格，下部放置编辑和删除按钮。
#### 实现思路
#### 表格
消费详情显示表格内容需要新建一个`DetailTableModel`，内容直接从数据库record表中查得,并在`updateData`方法中将记录按照时间顺序进行排序
#### 分类选择框
分类选择框内容直接与`CategoryComboBoxModel`连接，无需过多更改。为实现分类选择效果，这里新建`DetailService`来实现全部查找和分类查找两个方法。之后在监听器`DetailListener`中设置标志位以区分筛选和选择全部两个按钮，在刷新方法`updateData`中监听标志位，在刷新时区分查表方法。
#### 编辑和删除按钮  
下方的删除按键直接在监听器中调用`DetailService`中的删除方法即可，而编辑按钮则直接打开`EditFrame`。
#### 布局
该布局参考`SpendPanel`,分为上下两部分，上部用四行两列的方式进行布局，下部放置确定、取消按钮。
#### 实现思路
实现思路也与`SpendPanel`相似，不同之处在于编辑界面默认显示要与选定记录相同，且确定键的监听器`EditListener`的内容为更新。需要注意的是该面板默认显示内容需要与选定的记录相同，在`resetInput`方法中进行。因为种类也需要与所选记录一致，所以还需要在每次更新时对下拉框中的种类根据id进行排序，以保证在`JComboBox`的`setSelectedIndex`下可以选中与原记录相同的种类。
#### 最后效果
![Detail](/usr/blogImg/effect.png)

### 六、两个问题

* 类别删除后由于id自增长且不能重复造成的冲突
* 新增一笔后进入详情页，和详情页编辑后，日期会倒退一天