---
title: 基于OneNet云平台的智能IoT出行设备
date: 2022-01-24 16:44:53
tags: Java
categories: 
    - Java
    - Android
sticky: 100
---
# 功能设计
## 开箱检测与智能监测
### 开箱提醒与开箱记录
每一次打开箱子，都会被记录，用户可以通过配套的APP知道自己的箱子什么时候被打开或关闭过，同时也可以得知当前箱子的开闭状态。另外可以通过简单设置，当箱子被打开时，配套的APP发送通知给用户。
手机端弹框界面：
![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.2xganh9ffuc0.webp)![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.5iituf7e6fo0.webp)

### 环境较暗自动补光
当箱子处于打开状态，如果周围环境较暗，箱子的内置照明灯会亮起并照亮箱子内部。即使用户不得不在户外低照明的情况下开箱子，也不用一手提着手电一手找物品。另外，用户也可以手动打开照明灯进行照明。

### 开箱自动拍照记录开箱人与取放物品
开启箱子后，摄像头会自动开启并录像，拍摄的画面最终可以在APP中查看。通过把摄像头放置在合适的位置，使得正常开箱后能捕获到人脸。这样，用户不仅可以知道开箱时间，还能知道开箱人的脸部信息，以及取出了什么物品。
手机端界面：
![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.415lksnngfg0.webp)

## 旅行箱状态检测与分析
### 温湿度检测
系统能够把箱子的温湿度数据实时发送到用户APP上。如果用户在箱子中放置了如甜品，特殊药品等对温度湿度敏感的物品，则希望旅行箱可以提供相应信息，以便在条件不合适的时候采取其他办法。
用户可以自主设置温湿度阈值，app也会提供推荐阈值。当箱子内环境超出阈值时可以通过app提醒用户。在现实生活中，部分用户可能会把正常充电的设备放置在密封的旅行箱内导致旅行箱温度异常升高。APP针对这种情况设置异常阈值。提醒用户检查箱内安全。
手机端界面与警告弹框：
<table>
    <tr>
            <th>手机端界面</th>
            <th>警告弹框</th>
    </tr>
    <tr>
        <td><img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.4k6bg7mtx8k0.webp" /></td>
        <td><img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.7l3udrsz9840.webp" /></td>
    </tr>
</table>

### 箱子放置形状检测
系统能够把箱子的放置形状数据实时送到用户APP。正常情况下，放置形状几乎是不变的，但如果有人有意或者无意的摆动我们的箱子，APP会作出相应提醒。比如有人把我们的箱子撞翻了，或者有人挪动我们的箱子，用户都可以知道。考虑到有些物品是不能剧烈抖动的，用户还可以实时查看箱子的放置状态检测箱子是否发生倒立、侧翻、抖动等。
手机端界面：

![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.6kmc11tt96w0.webp)

## 位置与安全
### 定位服务与导航
用户可以通过APP了解用户当前位置和箱子的实时位置。即使意外丢失或者被盗，也能提高找回的可能性，定位误差小于50m。
在APP上，亦可根据导航提供的较为快捷的路线，较短时间内寻找到箱子。
手机端界面：
![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.2korin8rmrc0.webp)

### 箱子远离提醒
用户可以通过APP开启报警功能。当箱子离开用户可视范围时，箱子通过蜂鸣器警报，APP也会提醒用户。
手机端弹框界面：（远离时显示异常，再次靠近时显示正常）
![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.70mu9si5afk0.webp)![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.758k0brnkog0.webp)

### 声音寻箱
用户可以在手机上打开箱子的蜂鸣器。利用声音，用户可以快速定位箱子的位置。应用场景有：机场托运取行李，大巴行李仓取箱子，一时间找不到行李箱等。
### 消息显示
用户可以通过APP向箱子发送文字信息。在箱子意外落下时，可以把联系方式等推送到箱子外的显示屏上。如果有人发现，能够联系到主人。
手机端设置界面：（便于用户异步控制箱子）
![image](https://cdn.jsdelivr.net/gh/Carbda/image@master/image.4xciqu7bx7u0.webp)

# APP端实现
项目中负责的部分是Android端的APP开发，开发的主要逻辑是从OneNet云平台获取数据在手机端进行处理和展示，或是从手机端的APP进行命令的下达，通过OneNet平台的命令下发到旅行箱上的万耦开发板，以此实现对于开发板一端的控制。
## OnetNET平台交互
### 向OneNet查询设备信息
以查询设备信息为例，代码：
```java
/**
    * 向OneNet查询设备信息
    *
    * @param apiKey
    * @param deviceId:设备ID
    * @return
    */
public String getDevice(String deviceId,String apiKey) {
    String result="";
    BufferedReader in =null;
    try {
        String url = "http://api.heclouds.com/devices/"+deviceId;
        URL realUrl = new URL(url);
        //打开和URL之间的连接
        HttpURLConnection connection=(HttpURLConnection)realUrl.openConnection();
        //设置通用的请求属性
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Authorization",apiKey);
        //建立实际的连接
        connection.connect();
        // 定义 BufferedReader输入流来读取URL的响应
        in = new BufferedReader(new InputStreamReader(
                connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            result += line;
        }
    } catch (Exception e) {
        System.out.println("查询设备信息出现异常！ ");
    }
    // 使用finally块来关闭输入流
    finally {
        try {
            if (in != null) {
                in.close();
            }
        } catch (Exception e2) {
            System.out.println("IO close error !");
        }
    }
    return result;
}
```

### 查找设备数据点
这部分是APP端做开发板端数据展示的基础，例如要更新APP首页的温度等信息时要调用到refresh_tempreture()，而该方法中对于数据点的查询用到的就是下面的这个方法：
```java
/**
    * 查询设备数据点
    * @param deviceId:设备ID
    * @param apiKey
    * @param datastreamId:数据流id
    * @param start:提取数据点的开始时间
    * @param limit:限定本次请求最多返回的数据点数，默认为100
    * @return
    */
public String getDataPoints(String deviceId,String apiKey,String datastreamId,String start,int limit) {
    String result="";
    BufferedReader in =null;
    try {
        String url = "http://api.heclouds.com/devices/"+deviceId+"/datapoints?datastream_id="+datastreamId+"&limit="+limit;//"&start="+start
        URL realUrl = new URL(url);
        //打开和URL之间的连接
        HttpURLConnection connection=(HttpURLConnection)realUrl.openConnection();
        //设置通用的请求属性
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Authorization",apiKey);
        //建立实际的连接
        connection.connect();
        // 定义 BufferedReader输入流来读取URL的响应
        in = new BufferedReader(new InputStreamReader(
                connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            result += line;
        }
    } catch (Exception e) {
        e.printStackTrace();
        System.out.println("获取数据流出现异常！ ");
    }
    // 使用finally块来关闭输入流
    finally {
        try {
            if (in != null) {
                in.close();
            }
        } catch (Exception e2) {
            System.out.println("IO close error !");
        }
    }
    return result;
}
```

### 命令下达
命令下达的部分主要是对于开发板的灯、蜂鸣器、显示屏上文字的展示等部分通过命令下达以进行控制。
```java
/**
    * 下发命令
    * @param deviceId:设备ID
    * @param apiKey:token
    * @param timeout:响应最长等待时间
    * @param body:当报头为1时的命令命令内容，用不到时默认为""
    * @param flag:标识符，与报头一致
    * @param en:1打开，0关闭 ,用不到时默认为1
    * @return
    */
public String postCommand(String deviceId,String apiKey,int timeout,String body,int flag,int en)
{
    String result="";
    BufferedReader in =null;
    DataOutputStream out=null;
    try {
        String url = "http://api.heclouds.com/v1/synccmds?device_id="+deviceId+"&timeout="+timeout;
        URL realUrl = new URL(url);
        //打开和URL之间的连接
        HttpURLConnection connection=(HttpURLConnection)realUrl.openConnection();
        //设置通用的请求属性
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Authorization",apiKey);
        connection.setDoInput(true);
        connection.setDoOutput(true);
        //建立实际的连接
        connection.connect();
        //下发命令
        out=new DataOutputStream(connection.getOutputStream());
        switch (flag){
            case 1: //10行每行20个字符，目前只实现了一页
                //以“字节数组”形式发送给服务器信息、
                byte[] data=body.getBytes();
                out.writeByte(1);//报头
                out.writeByte(1);//当前页数
                out.writeByte(1);//总页数
                out.write(data);
                out.writeByte(0);
                break;
            case 2:
                out.writeByte(2);//报头
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            case 3:
                out.writeByte(3);//报头
                out.writeByte(0);
                out.writeByte(0);
                break;
            case 4:
                out.writeByte(11);
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            case 5:
                out.writeByte(5);
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            case 6:
                out.writeByte(6);
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            case 7:
                out.writeByte(7);
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            case 8:
                out.writeByte(8);
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            case 10:
                out.writeByte(10);
                if(en==1) out.writeByte(1);
                else out.writeByte(0);
                out.writeByte(0);
                break;
            default:
                throw new IllegalStateException("Unexpected value: " + flag);
        }


        // 定义 BufferedReader输入流来读取URL的响应
        in = new BufferedReader(new InputStreamReader(
                connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            result += line;}
        System.out.println(result);
    } catch (Exception e) {
        e.printStackTrace();
        System.out.println("命令下发异常！ ");
    }

    // 使用finally块来关闭输入流
    finally {
        try {
            if (in != null) {
                in.close();
            }
        } catch (Exception e2) {
            e2.printStackTrace();
            System.out.println("IO close error !");
        }
    }
    System.out.println(result);
    return result;
}
```
### 文件获取
在APP端中文件获取方法唯一的作用就是获取开箱时候的图片。
```java
/**
    * 获取文件(无需deviceid)
    * @param apiKey
    * @param box_id:开箱id(第几次开箱)
    * @param zhen:第几帧
    * @return
    */
public byte[] getFile(String apiKey,int box_id,int zhen) {
    BufferedReader in =null;
    byte []datas=null;
    try {
        String url = "http://api.heclouds.com"+"/bindata"+"/"+index_front+box_id+"-"+zhen+".jpg";
        URL realUrl = new URL(url);
        //打开和URL之间的连接
        HttpURLConnection connection=(HttpURLConnection)realUrl.openConnection();
        //设置通用的请求属性
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Authorization",apiKey);
        //建立实际的连接
        connection.connect();
        //定义 BufferedReader输入流来读取URL的响应
        InputStream is = connection.getInputStream();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        // 操作（分段读取）
        byte[] flush = new byte[1024];// 缓冲容器
        int len = -1;// 接收长度
        while ((len = is.read(flush)) != -1) {
            baos.write(flush, 0, len);
        }
        baos.flush();
        // 获取数据
        datas = baos.toByteArray();
    }
    catch (Exception e) {
        e.printStackTrace();
        System.out.println("获取文件出现异常！ ");
    }
    // 使用finally块来关闭输入流
    finally {
        try {
            if (in != null) {
                in.close();
            }
        } catch (Exception e2) {
            System.out.println("IO close error !");
        }
    }
    return datas;
}
```

## 数据更新
以APP首页的温度、湿度、光强，以及箱子的开闭，开发板的开关情况为例，这些都是在APP端的首页进行了信息展示，对于这些信息的数据更新在Android端是不能直接在Activity里生成一个Thread来进行实时更新的，那样会闪退，所以需要对于一个页面建立Handler类，在该类里面进行数据更新。

### 线程中的run()方法
该部分的逻辑是不断地发送Message给Handler进行处理，case为0时是对于开发板的六轴的数据进行获取，在case为1~3时是对于温度、湿度和光强进行更新，在case为4时则是对于箱子的开关状态进行监控。
```java
@Override
public void run() {
    while (true){
        //一个线程内发消息不能用同一个message
        Message message0 = new Message();
        Message message1 = new Message();
        Message message2 = new Message();
        Message message3 = new Message();
        Message message4 = new Message();
        for(int i=0;i<=4;i++){
            //使能有效就发message，否则不发
            switch (i){
                case 0:
                    message0.what = 0;
                    handler.sendMessage(message0);
                    break;
                case 1:
                    if(temperature_en.en==1) {
                        message1.what=1;
                        handler.sendMessage(message1);
                    }
                    break;
                case 2:
                    if(guang_en.en==1) {
                        message2.what=2;
                        handler.sendMessage(message2);
                    }
                    break;
                case 3:
                    if(shidu_en.en==1) {
                        message3.what=3;
                        handler.sendMessage(message3);
                    }
                    break;
                case 4:
                    message4.what = 4;
                    handler.sendMessage(message4);
                    break;
                default:
                    throw new IllegalStateException("Unexpected value: " + i);
            }
        }
        try {
            Thread.sleep(1000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

### MyHandler中的处理
这里主要注意到的是case为4时对于开闭状态的更改并不是直接进行更改，因为在实际测试中发现从开发板到OneNET平台的红外检测数据会有波动，这可能是红外接收和发射的装置没有完全对准好导致的结果，这会让APP端的状态更改也发生波动，所以在APP端进行了处理，在数据稳定不变几秒之后才进行箱子开闭状态的更新，防止手机端的频繁弹窗提醒。
其他的部分就和上述差不多，case为0时是对于开发板的六轴的数据进行获取，在case为1~3时是对于温度、湿度和光强进行更新。
**handleMessage方法：**
```java
@Override
public void handleMessage(@NonNull Message msg) {
    super.handleMessage(msg);
    //更新数据，刷新UI
    switch(msg.what){
        case 0:
            String str = api.getDevice(deviceId,apiKey);
            boolean temponline = judgeOnline(str);
            //考虑不稳定情况，记录不稳定开始时间
            if (temponline != online && !unstable_online) {
                utime_online_1 = System.currentTimeMillis();
                unstable_online = true;
            }
            //若不稳定，记录当前时间
            if (unstable_online) utime_online_2 = System.currentTimeMillis();
            //若由不稳定转向稳定
            if (unstable_online && temponline == online) unstable_online = false;
            //确定改变状态
            if (temponline != online  && unstable_online && utime_online_2-utime_online_1 >= 1000) {
                onlineDialog(temponline);
                onlineNotification(temponline);
                String tempstr1="设备: <font color='#FF0000'><small>正常</small></font>";
                String tempstr2="设备: <font color='#FF0000'><small>异常</small></font>";
                if (temponline == true) t4.setText(Html.fromHtml(tempstr1));
                else t4.setText(Html.fromHtml(tempstr2));
                online = temponline;
                unstable_online = false;
            }

            //如果箱子形状发生改变要引导用户去查看箱子摆放状态，每3秒检查一次
            api.refresh_acce(deviceId,apiKey);
            acce_x = -api.acce_x;
            acce_y = api.acce_y;
            acce_z = api.acce_z;
            new_degree = (float)(Math.atan2((double)acce_x, (double)acce_z) * 180 / Math.PI);
            if(acce_y>700 && acce_y<1200) new_flag = 1;
            else if(acce_y>300 && acce_y<=700) new_flag = 2;
            else if(acce_y>-1200 && acce_y<-800) new_flag = 3;
            else if(acce_y>=-800 && acce_y<-400) new_flag = 4;
            else new_flag = 0;
            //不稳定的时候更新时间，直到与上一次差3000ms
            if(unstable_location) {
                utime_location_2 = System.currentTimeMillis();
                if(utime_location_2 - utime_location_1 >=3000){
                    unstable_location = false;
                }
            }
            //稳定的时候与old比较
            else
            {
                boolean same = true;
                if(new_flag != old_flag) same = false;
                if(!same){
                    AlertDialog dialog = new AlertDialog.Builder(ma)
                            .setTitle("箱子状态")//标题
                            .setMessage("箱子状态发生改变，请前往查看")//内容
                            .setIcon(R.drawable.box_2)
                            .create();
                    dialog.show();
                }
                //更新状态和time1
                utime_location_1  = System.currentTimeMillis();
                old_degree = new_degree;
                old_flag = new_flag;
                unstable_location = true;
            }
            break;
        case 1:
            api.refresh_temperature(deviceId,apiKey);
            t1.setText("温度: "+api.temperature_zheng+"."+api.temperature_xiao);
            showDialog(1,api.temperature_zheng,tem_max,tem_min);
            break;
        case 2:
            api.refresh_guang(deviceId,apiKey);
            t2.setText("光强: "+api.guang_zheng+"."+api.guang_xiao);
            break;
        case 3:
            api.refresh_shidu(deviceId,apiKey);
            t3.setText("湿度: "+api.shi_du_zheng+"."+api.shi_du_xiao);
            showDialog(3,api.shi_du_zheng,shidu_max,shidu_min);
            break;
        case 4:
            api.refresh_isopen(deviceId,apiKey);
            int tempisopen = api.is_open;
            //考虑不稳定情况，记录不稳定开始时间
            if (tempisopen != is_open && !unstable_open) {
                utime_open_1 = System.currentTimeMillis();
                unstable_open = true;
            }
            //若不稳定，记录当前时间
            if (unstable_open) utime_open_2 = System.currentTimeMillis();
            //若由不稳定转向稳定
            if (unstable_open && tempisopen == is_open) {
                unstable_open = false;
            }
            //确定改变状态
            if (tempisopen != is_open && unstable_open && utime_open_2-utime_open_1 >= 1000) {
                openDialog(tempisopen);
                openNotification(tempisopen);
                String tempstr1="箱子: <font color='#FF0000'><small>打开</small></font>";
                String tempstr2="箱子: <font color='#FF0000'><small>关闭</small></font>";
                if (tempisopen == 1) t5.setText(Html.fromHtml(tempstr1));
                else t5.setText(Html.fromHtml(tempstr2));
                is_open = tempisopen;
                unstable_open = false;
            }
            break;
        default:
            throw new IllegalStateException("Unexpected value: " + msg.what);
    }
    //若关闭检测则不显示数据
    if (temperature_en.en == 0) t1.setText("温度:");
    if (guang_en.en == 0) t2.setText("光强:");
    if (shidu_en.en == 0) t3.setText("湿度:");
}
```

## 开箱视频
开箱时树莓派的摄像头会对开箱者拍下一定数量的照片，而这些照片会上传到OneNET云平台之后，由手机端的文件读取方法读取到APP端进行展示，展示的方法就是每隔几毫秒用drawImage方法来展示图片以达到开箱“视频”的效果。
### 线程中的run()方法
这里就比较简单了，因为只涉及到一个操作也就是从OneNET平台读数据，所以只要每隔25秒就发送一次Message就可以了。
```java
@Override
public void run() {
    while(true){
        try {
            //这个message必须每次new一次，不能发同一个message，会冲突
            //每1秒更新一次开箱总id,并以message形式发送给handler
            Message message = new Message();
            message.what = 5;
            myHandler_camera.sendMessage(message);
            Thread.sleep(25);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

### MyHandler_camera中的处理方法
这里同样简单，因为对于照片的处理是在MyView_camera中进行
```java
@Override
public void handleMessage(@NonNull Message msg) {
    super.handleMessage(msg);
    //实时更新
    //如果开箱id发生了更新那么需要重新绘制
    if (msg.what == 5) {
        //刷新view展示新的照片
        if(box_id.state == 0)
            myView_camera.invalidate();
    }
}
```

### MyView_camera中的处理
**首先是onDraw()方法：**
可以看到里面用到了getFile方法来从OneNET云平台获取图片，而获取到的文件事实上是以byte数组形式来让手机端APP接收的，所以需要用一个方法byte2image方法来转换成Bitmap类型（这个类型和电脑java里的BufferedImage很像）
```java
protected void onDraw(Canvas canvas) {
    //照片的字节数组
    Bitmap image = null;
    //画笔
    Paint p = new Paint();
    //byte数组转换为bitmap
    image = byte2image(api_edp.getFile(Device.apiKey_Camera, box_id.value, box_id.en));//获取照片
    //最后一张3
    if (box_id.en >= box_id.sum) {
        box_id.state = 1;
        return;
    }
    else if (image == null) {
        box_id.en++;
        return;
    }
    imageView.setImageBitmap(image);
    box_id.en++;//下一帧
    box_id.state = 0;
}
```
**byte数组转bitmap：**
```java
//byte数组转bitmap
public Bitmap byte2image(byte[] dataImage) {
    BitmapFactory.Options options = new BitmapFactory.Options();
    options.inSampleSize = 2;
    if (dataImage != null){
        Bitmap bitmap = BitmapFactory.decodeByteArray(dataImage,0,dataImage.length,options);
        return bitmap;
    }
    else return null;
}
```

## 命令下达
命令下达在APP端有对于开发板的显示屏信息的更新、蜂鸣器控制、灯光控制等功能，下面进行部分代码的展示。
### 与前端的部件相连
```java
//与前端部件相连
eText=findViewById(R.id.edittext);
b1 = findViewById(R.id.btn1);
b3 = findViewById(R.id.btn3);
s2 = findViewById(R.id.sw2);    s2.setChecked(Data.fengmingqi);
s4 = findViewById(R.id.sw4);    s4.setChecked(Data.zhaomingdeng);
s5 = findViewById(R.id.sw5);    s5.setChecked(Data.wendujiance);
s6 = findViewById(R.id.sw6);    s6.setChecked(Data.shidujiance);
s7 = findViewById(R.id.sw7);    s7.setChecked(Data.guangqiangjiance);
s8 = findViewById(R.id.sw8);    s8.setChecked(Data.liuzhoujiance);
s10 = findViewById(R.id.sw10);  s10.setChecked(Data.wifi_isopen);
s11 = findViewById(R.id.sw11);  s11.setChecked(Data.lowpower_isopen);
s9 =findViewById(R.id.sw9);     s9.setChecked(Data.lanya);
```
### postCommand方法的调用
**这里以更新显示屏上的文字为例：**
首先前面eText已经和前端部件edittext进行了绑定，因此在设置界面输入了相关文字（由于开发板只能显示英文所以输入的是英文），而b1和部件btn1进行了绑定，在点击btn1时监听器调用onClick方法，调用了postCommand方法，将eText.getText.toString()传入该方法，从而完成显示屏端的信息更新。
```java
//显示信息
b1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        api.postCommand(deviceId,apiKey,30,eText.getText().toString(),1,1);
    }
});
```

## 位置更新
位置更新部分使用了高德地图的API来完成相关功能：
### 线程中的run()方法
**线程部分代码依然简单：**
```java
@Override
    public void run() {
        while(true){
            try {
                //这个message必须每次new一次，不能发同一个message，会冲突
                Message message=new Message();
                message.what=4;
                handler.sendMessage(message);
                Thread.sleep(10000);
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }
    }
```

### MyHandler_location
这里要注意的是，标记点的更新首先需要将之前的标记点marker进行remove操作来移除，否则会出现标记点的重复，首先调用到refresh_location方法将接收到的开发板坐标进行更新，之后将数据转换成double类型，**LatLng类是记录坐标点的类**，将该类用到高德地图API中AMap的**addMarker方法**，将刚刚的数据作为参数传入，即可更新开发板在地图上的位置。
```java
@Override
    public void handleMessage(@NonNull Message msg) {
        super.handleMessage(msg);
        int num = msg.what;
        //实时的位置更新,删除原来的marker点显示新的maker点
        if(msg.what==4){
            System.out.println("i 'm flag");
            if(marker!=null)
            marker.remove();
            //正常情况
            api.refresh_location(deviceId,apiKey);
            latLng = new LatLng(get_location_double(api.wz,api.wx),get_location_double(api.jz,api.jx));
            marker = amap.addMarker(new MarkerOptions().position(latLng).title("旅行箱").snippet("旅行箱"));
            System.out.println("经度为："+get_location_double(api.jz,api.jx));
            System.out.println("纬度为："+get_location_double(api.wz,api.wx));
        }
    }
```
### MainActivity
这部分直接放代码，主要是这个页面的一些初始化操作：
```java
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    supportRequestWindowFeature(Window.FEATURE_NO_TITLE);
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        MainActivity.setStatusBarColor(this,0xFF6BA821);
    }
    setContentView(R.layout.activity_main3);
    //显示地图
    //获取地图控件引用
    mapview = (MapView) findViewById(R.id.map);
    //在activity执行onCreate时执行mMapView.onCreate(savedInstanceState)，创建地图
    mapview.onCreate(savedInstanceState);
    if(aMap==null)
    aMap=mapview.getMap();
    //handler类实例化
    myhandler_location=new Myhandler_location(api,aMap);
    //线程Thread实例化
    refreshThread_location=new RefreshThread_location(myhandler_location);
    //开启定位权限
    //看权限是否开启
    if(ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED){//未开启定位权限
        //开启定位权限,200是标识码
        ActivityCompat.requestPermissions(this,new String[]{Manifest.permission.ACCESS_FINE_LOCATION},200);
    }else{
        //开始定位
        Toast.makeText(this,"已开启定位权限",Toast.LENGTH_LONG).show();
    }
    //开启定位蓝点
    myLocationStyle = new MyLocationStyle();//初始化定位蓝点样式类
  	myLocationStyle.myLocationType(MyLocationStyle.LOCATION_TYPE_LOCATION_ROTATE);//连续定位、且将视角移动到地图中心点，定位点依照设备方向旋转，并且会跟随设备移动。（1秒1次定位）如果不设置myLocationType，默认也会执行此种模式。
    myLocationStyle.interval(2000); //设置连续定位模式下的定位间隔，只在连续定位模式下生效，单次定位模式下不会生效。单位为毫秒。
    //myLocationStyle.myLocationIcon(BitmapDescriptorFactory.fromResource(R.drawable.gps_point));
    myLocationStyle.myLocationType(MyLocationStyle.LOCATION_TYPE_MAP_ROTATE);
    aMap.setMyLocationStyle(myLocationStyle);//设置定位蓝点的Style
    aMap.getUiSettings().setMyLocationButtonEnabled(true);//设置默认定位按钮是否显示，非必需设置。
    aMap.setMyLocationEnabled(true);// 设置为true表示启动显示定位蓝点，false表示隐藏定位蓝点并不进行定位，默认是false。
    //开启更新位置的线程
    refreshThread_location.start();
}
```