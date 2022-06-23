# -

测试第一个仓库

用java链接mysql数据库实现对数据库的增删改查，并输出到控制台，做题判分
1.
examclass类{定义一个试题类，以试题类为单位进行存储，属性分别为：
（id，tatil，optionA,optionB,optionC,optionD,answer,score,
delflag[这里的delflag属性设为int类型，0表示不删除状态，默认为0，非0表示删除状态]）
并重写tostring方法}：

代码如下：





    public class examclass {
        private String id;
        private String tatil;
        private String optionA;
        private String optionB;
        private String optionC;
        private String optionD;
        private String answer;
        private int score;
        private int delflag;
            //试题类的空参构造器和有参构造器
        public examclass(){
            super();
            }
        public  examclass(String id,String tatil,String optionA,String optionB,
            String optionC,String optionD,String answer,int score,int delflag){
            super();
            this.id=id;
            this.answer=answer;
            this.optionA=optionA;
            this.optionB=optionB;
            this.optionC=optionC;
            this.optionD=optionD;
            this.tatil=tatil;
            this.score=score;
            this.delflag=delflag;
             }

    public int getDelflag() {
        return delflag;
    }

    public void setDelflag(int delflag) {
        this.delflag = delflag;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getTatil() {
        return tatil;
    }

    public void setTatil(String tatil) {
        this.tatil = tatil;
    }

    public String getOptionA() {
        return optionA;
    }

    public void setOptionA(String optionA) {
        this.optionA = optionA;
    }

    public String getOptionB() {
        return optionB;
    }

    public void setOptionB(String optionB) {
        this.optionB = optionB;
    }

    public String getOptionC() {
        return optionC;
    }

    public void setOptionC(String optionC) {
        this.optionC = optionC;
    }

    public String getOptionD() {
        return optionD;
    }

    public void setOptionD(String optionD) {
        this.optionD = optionD;
    }

    public String getAnswer() {
        return answer;
    }

    public void setAnswer(String answer) {
        this.answer = answer;
    }

    public int getScore() {
        return score;
    }

    public void setScore(int score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return
                "第'" + id + "题:\t\n" +
                tatil + "\t\n" +
                "A:" + optionA +"\t\n" +
                "B:" + optionB + "\t\n"+
                "C:" + optionC +"\t\n"+
                "D:" + optionD + "\t\n"
               ;
        }
    }
2.
定义sysexam类，实现用jdbc对数据库的链接，并将sql查询结果放入Resultset集保存，
用Resultset.getXXX()方法将数据赋值给变量，并利用变量进行创建试题类对象存放在
数据结构ArrayList（注意泛型）存储：

代码如下：
















        import java.sql.*;
        import java.util.ArrayList;

        public class sysexam {

            public ArrayList<examclass> getQuestion() {
            //选择数据结构ArrayList作为存储试题的集合
            ArrayList<examclass> list=new ArrayList<>();
            /*
            *jdbc进行链接数据库操作
            *1.注册Driver驱动：使用Class.forName("com.mysql.jdbc.Driver")进行注册
            *2.链接mysql库：Connection conn=DriverManager.getConnection("url","user","pwd")
            *3.创建Statement类的对象：使用链接库的对象的createStatement()方法创建
            *4.使用Statement对象的execute方法进行sql操作
            *5.关闭资源
            */
            try {
                Class.forName("com.mysql.jdbc.Driver");
                Connection conn= DriverManager.getConnection("jdbc:mysql://localhost:3306/sys","root","root");
                Statement state= conn.createStatement();
                ResultSet res= state.executeQuery("select * from exam");
                while(res.next()){
                    String id =res.getString(1);
                    String tatil =res.getString(2);
                    String a =res.getString(3);
                    String b =res.getString(4);
                    String c =res.getString(5);
                    String d =res.getString(6);
                    String anw =res.getString(7);
                    int score =res.getInt(8);
                    list.add(new examclass(id,tatil,a,b,c,d,anw,score,0));
                    }

                    res.close();
                    state.close();
                    conn.close();
            } catch (ClassNotFoundException | SQLException e) {
                e.printStackTrace();
            }
                return list;
            }
        }


3.
定义exammanage类，实现对题库的增、删、改、查操作：
代码如下：



        import java.sql.Connection;
        import java.sql.DriverManager;
        import java.sql.SQLException;
        import java.sql.Statement;
        import java.util.ArrayList;
        import java.util.Random;
        import java.util.Scanner;

        import com.mysql.jdbc.ResultSet;

        public class exammanage {

        public ArrayList<examclass> printexam(ArrayList<examclass> list, int total) {

            ArrayList<examclass> listb=new ArrayList<examclass>();
            ArrayList<Integer> listNumber=new ArrayList<Integer>();
            sysexam sy=new sysexam();
            Random ran=new Random();
            int i=-1;
            while(listNumber.size()<total){
                i=ran.nextInt(list.size());
                if(!listNumber.contains(i)&&list.get(i).getDelflag()==0){
                    listNumber.add(i);
                }
            }
            for (Integer n:listNumber){
                listb.add(list.get(n));
            }
            return listb;

        }
        public Integer doexam(ArrayList<examclass> listb) {
            int score=0;
            for(examclass q:listb){
                System.out.println(q);
                Scanner scan=new Scanner(System.in);
                System.out.println("-----------");
                System.out.println("--请输入您的答案：");
                String youranswer=scan.next();
                if(youranswer.equals(q.getAnswer())){
                    System.out.println("恭喜回答正确");
                    score=score+10;
                }else{
                    System.out.println("对不起回答错误");
                }

            }
            return score;

        }

        public void addexam(String labname, String user, String pwd) {
            try {
                Class.forName("com.mysql.jdbc.Driver");
                Connection conn = DriverManager.getConnection(
                        "jdbc:mysql://localhost:3306/" + labname, user, pwd);
                Statement state = conn.createStatement();
                ResultSet result = (ResultSet) state
                        .executeQuery("select * from exam");
                Scanner scan=new Scanner(System.in);
                System.out.println("--请输入题号(4位)：");
                int id=scan.nextInt();
                System.out.println("--请输入题干：");
                String title=scan.next();
                System.out.println("--请输入选项A：");
                String opA=scan.next();
                System.out.println("--请输入选项B：");
                String opB=scan.next();
                System.out.println("--请输入选项C：");
                String opC=scan.next();
                System.out.println("--请输入选项D：");
                String opD=scan.next();
                System.out.println("--请输入标准答案：");
                String answer=scan.next();
                int delflag=0;
                String s="insert into exam values('"+id+"','"+title+"','"+opA
                        +"','"+opB+"','"+opC+"','"+opD+"','"+answer+"','"+delflag+"')";

                state.executeUpdate(s);

                result.close();
                state.close();
                conn.close();

            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            } catch (SQLException e) {
                e.printStackTrace();
            }

        }
        public void changeexam(String labname, String user, String pwd) {
            try {
                Class.forName("com.mysql.jdbc.Driver");
                Connection conn = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/" + labname, user, pwd);
                Statement state = conn.createStatement();
                ResultSet result = (ResultSet) state
                    .executeQuery("select * from exam");
                Scanner scan=new Scanner(System.in);
                System.out.println("请输入要修改的题号(4位)：");
                int id=scan.nextInt();
                System.out.println("请输入选择修改的选项：");
                System.out.println("1题干，2选项A,3选项B,4选项C,5选项D,6答案");
                int option=scan.nextInt();
                String changer=null;
                switch (option){
                    case 1:changer="tatil";break;
                    case 2:changer="optionA";break;
                    case 3:changer="optionB";break;
                    case 4:changer="optionC";break;
                    case 5:changer="optionD";break;
                    case 6:changer="answer";break;
                    default:System.out.println("输入错误");
                }
                System.out.println("请修改：");
                String changefinal=scan.next();
                String s="update exam set "+changer+ "='" +changefinal+ "' where(id='" +id+ "')";

                state.executeUpdate(s);

                result.close();
                state.close();
                conn.close();

            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            } catch (SQLException e) {
                e.printStackTrace();
            }

        }
        public void selexam (String labname, String user, String pwd) {
            try {
                Class.forName("com.mysql.jdbc.Driver");
                Connection conn = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/" + labname, user, pwd);
                Statement state = conn.createStatement();
                Scanner scan=new Scanner(System.in);
                System.out.println("请输入要查找的题号(4位)：");
                int id=scan.nextInt();
                String s="select * from exam where(id='"+id+"')";
                ResultSet result=(ResultSet) state.executeQuery(s);
                while(result.next()){
                    System.out.println( "第'" + id + "题:\t\n" +
                                result.getString(2) + "\t\n" +
                                "A:" + result.getString(3) +"\t\n" +
                                "B:" + result.getString(4)+ "\t\n"+
                                "C:" + result.getString(5)+"\t\n"+
                                "D:" + result.getString(6)+ "\t\n"+
                                "答案："+result.getString(7));
                }

            result.close();
            state.close();
            conn.close();

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }
    public void delexam (ArrayList<examclass> list){
        Scanner scan=new Scanner(System.in);
        System.out.println("请输入要删除的题号(4位)：");
        String id=scan.next();

        list.forEach(h->{
            if (h.getId().equals(id)){
                h.setDelflag(1);
                System.out.println("删除成功");
            }
        });
        System.out.println("试题不存在");

    }
    public void meau(ArrayList<examclass> list){
        System.out.println("1.增加试题，2.修改试题，3.删除试题，4.查找试题");
        Scanner scan=new Scanner(System.in);
        int option=scan.nextInt();
        switch (option){
            case 1:addexam("sys","root","root");break;
            case 2:changeexam("sys","root","root");break;
            case 3:delexam(list);break;
            case 4:selexam("sys","root","root");break;
            }
        }
    }



4.
定义text类，进行对程序的测试运行：
代码如下：


    import java.util.ArrayList;
    import java.util.Scanner;

    public class text {
        public static void main(String[] args) {
            int userscore=0;
            int examnum=0;
            exammanage em=new exammanage();
            sysexam sy=new sysexam();
            ArrayList<examclass> list=new ArrayList<>(sy.getQuestion());
            Scanner scan=new Scanner(System.in);
            System.out.println("**********考试系统************");
            while(true) {
                System.out.println("1考试，2试题管理，3退出");
                int option = scan.nextInt();
                switch (option) {
                    case 1:
                        while (true) {
                            System.out.println("请输入题量:");
                            examnum = scan.nextInt();
                            userscore = em.doexam(em.printexam(list, examnum));
                            System.out.println("你的总分为:" + userscore);
                            System.out.println("1.返回上一级菜单，2.继续答题");
                            int opt = scan.nextInt();
                            if (opt == 1) {
                                break;
                            } else if (opt == 2) {
                                continue;
                            } else {
                                System.out.println("输入错误");
                                break;
                            }

                        }break;
                    case 2:
                        em.meau(list);
                        break;
                    case 3:
                        System.exit(0);
                        break;
                    default:
                        System.out.println("输入错误");
                        System.exit(0);
                }

            }
        }
    }
