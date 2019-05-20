![MyTem icon](https://github.com/GeminiJay/MyTem/blob/master/app/src/main/res/drawable/tumb.png) 
# MyTem 
  
## 目录   
* [项目介绍](#项目介绍)  
* [使用说明](#使用说明)  
  * [获取代码](#获取代码) 
  * [操作流程](#操作流程)   
  * [所用Jar包及关键代码](#所用Jar包及关键代码) 
	* [包](#包)  
   	* [关键代码](#关键代码)  
* [其他](#其他)  
  
<a name="项目介绍"></a>  
## 项目介绍  
  
*MyTem* 作为手机与蓝牙模块数据交互的毕设项目通过蓝牙数据传输实现了六路加热片的状态控制以及加热片温度的实时显示<br>  
 
<a name="使用说明"></a>  
## 使用说明  
  
<a name="获取代码"></a>  
#### 获取代码  
  
* MyTem项目主页: <https://github.com/GeminiJay/MyTem>    
git上的会持续更新。  
  
另外你也可以通过git的Maven库获取依赖，或者自己编译jar包。
  
<a name="操作流程"></a>  
#### 操作流程   
  
     1. 主页面操作  
		
        1）点击开/关按钮，并点击相关部位文本转盘可显示实时读数。
		    2）点击智能恒温可实现所有部位的恒温控制。
  
     2. 设置页面操作 
	 
	    1）指定温度设置：点击相应部位开关，然后设置相应值
		    2）自定义温度设置：点击相应部位文本，然后拖拉滑杆设置预期温度值。
	 
     
     3. 蓝牙页面操作 
	 
	    1）搜索蓝牙
		    2）连接蓝牙，并返回
   
<a name="所用Jar包及关键代码"></a>  
### 所用Jar包及关键代码
##### 包  
1. import android.bluetooth.BluetoothAdapter; 
2. import org.greenrobot.eventbus.EventBus;
3. import com.example.mainsmartcontroltem.view.NoiseboardView.(自己制作UI布局)
##### 关键代码 
```  
    /**
     * 蓝牙通信管理
     */
    private class ConnetThread extends Thread {
        public void run() {

            try {

                InputStream inputStream = btSocket.getInputStream();
                OutputStream outputStream = btSocket.getOutputStream();
                byte[] data = new byte[1024];
                int len;

                while (true) {

                    try {
                        try {
                            Thread.sleep(100);//等待0.5秒，让数据接收完整
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        len = inputStream.read(data);
                        result = new String(data,0,len);
                        //result = String.valueOf(len);//new String(data, 0,len);
                        //result = result.substring(0, len);
                        Message msg = new Message();
                        msg.what = MESSAGE_READ;
                        msg.obj = result;
                        handler.sendMessage(msg);
                        sendConmand = mysendCMD.sendmidCommand + "#";
                        Log.v("发送:", sendConmand);
                        outputStream.write(sendConmand.getBytes(StandardCharsets.UTF_8));

                    } catch (IOException e) {
                        e.printStackTrace();
                        break;
                    }
                }
            }
            catch (IOException e) {
                e.printStackTrace();
            }
        }


    }
    /*消息机制处理*/
    @SuppressLint("HandlerLeak")
    protected Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case MESSAGE_READ:
                    result = (String) msg.obj;
                    if (EventBus.getDefault().hasSubscriberForEvent(MessageEvent.class)) {
                        EventBus.getDefault().post(new MessageEvent(result));
                    }

                    //Toast.makeText(getApplicationContext(), sendConmand, Toast.LENGTH_SHORT).show();

            }
        }
    };
```  
<a name="其他"></a>  
## 其他  
    ``` 
      None
    ``` 
