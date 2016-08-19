# Thumbnail extraction while uploading

업로딩 시간절감과 사용자들의 업로드 대기시간을 줄이기 위해 업로드 중 썸네일을 추출해 UI에 반영할 수 있는 업로더를 개발하게 되었다.

## FFMEPG 설치
 - 참고
 > - http://wiki.razuna.com/display/ecp/FFMpeg+Installation+on+CentOS+and+RedHat
 > - http://wiki.razuna.com/display/ecp/FFMpeg+Installation+on+CentOS+and+RedHat#FFMpegInstallationonCentOSandRedHat-Installtheadditionalrepo
>  - http://yujuwon.tistory.com/entry/CentOS-ffmpeg-%EC%84%A4%EC%B9%98

 - Repository 업데이트
> yum -y update

 - 필수 패키지 설치
> yum install glibc gcc gcc-c++ autoconf automake libtool git make nasm pkgconfig
> yum install SDL-devel a52dec a52dec-devel alsa-lib-devel faac faac-devel faad2 faad2-devel
> yum install freetype-devel giflib gsm gsm-devel imlib2 imlib2-devel lame lame-devel libICE-devel libSM-devel libX11-devel
> yum install libXau-devel libXdmcp-devel libXext-devel libXrandr-devel libXrender-devel libXt-devel
> yum install libogg libvorbis vorbis-tools mesa-libGL-devel mesa-libGLU-devel xorg-x11-proto-devel zlib-devel
> yum install libtheora theora-tools
> yum install ncurses-devel
> yum install libdc1394 libdc1394-devel
> yum install amrnb-devel amrwb-devel opencore-amr-devel 

 - xvid 설치
> cd /opt
> wget http://downloads.xvid.org/downloads/xvidcore-1.3.2.tar.gz
> tar xzvf xvidcore-1.3.2.tar.gz
> cd xvidcore/build/generic
> ./configure --prefix="$HOME/ffmpeg_build"
> make
> make install

 - LibOgg 설치
> cd /opt
> wget http://downloads.xiph.org/releases/ogg/libogg-1.3.1.tar.gz
> tar xzvf libogg-1.3.1.tar.gz
> cd libogg-1.3.1
> ./configure --prefix="$HOME/ffmpeg_build" --disable-shared
> make
> make install

 - Libvorbis 설치
> cd /opt
> wget http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.4.tar.gz
> tar xzvf libvorbis-1.3.4.tar.gz
> cd libvorbis-1.3.4
> ./configure --prefix="$HOME/ffmpeg_build" --with-ogg="$HOME/ffmpeg_build" --disable-shared
> make
> make install

 - Libtheora 설치
> cd /opt
> wget http://downloads.xiph.org/releases/theora/libtheora-1.1.1.tar.gz
> tar xzvf libtheora-1.1.1.tar.gz
> cd libtheora-1.1.1
> ./configure --prefix="$HOME/ffmpeg_build" --with-ogg="$HOME/ffmpeg_build" --disable-examples --disable-shared --disable-sdltest    --disable-vorbistest
> make
> make install

 - Yasm 설치
> yum remove yasm
> cd /opt
> wget http://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz
> tar xzfv yasm-1.2.0.tar.gz
> cd yasm-1.2.0
> ./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin"
> make
> make install
> export "PATH=$PATH:$HOME/bin" 

 - Libvpx 설치
> cd /opt
> git clone https://chromium.googlesource.com/webm/libvpx.git
> cd libvpx
> git checkout tags/v.1.3.0
> ./configure --prefix="$HOME/ffmpeg_build" --disable-examples
> make
> make install

 - X264 설치 
> cd /opt
> git clone git://git.videolan.org/x264.git
> cd x264
> ./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin" --enable-static 
> make
> make install

 - 라이브러리 설정
> export LD_LIBRARY_PATH=/usr/local/lib/
> echo /usr/local/lib >> /etc/ld.so.conf.d/custom-libs.conf
> ldconfig

 - FFmpeg 컴파일(the configure options have to be on one line)
> cd /opt
> git clone git://source.ffmpeg.org/ffmpeg.git
> cd ffmpeg
> git checkout release/2.5
> PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig"
> export PKG_CONFIG_PATH
>./configure --prefix="$HOME/ffmpeg_build" --extra-cflags="-I$HOME/ffmpeg_build/include" --extra-ldflags="-L$HOME/ffmpeg_build/lib" --bindir="$HOME/bin" \
--extra-libs=-ldl --enable-version3 --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libvpx --enable-libfaac \
--enable-libmp3lame --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libvo-aacenc --enable-libxvid --disable-ffplay \
--enable-gpl --enable-postproc --enable-nonfree --enable-avfilter --enable-pthreads
> make
> make install


## RabbitMQ 설치 및 클러스터 구성
 - Elang 설치 
>mkdir -p ~/apps
>cd ~/apps
>wget http://10.114.81.109/install/asp-rabbit/otp_src_17.4.tar.gz
>tar -xvf otp_src_17.4.tar.gz
>cd otp_src_17.4
>./configure --without-termcap --prefix=/home1/irteam/apps/otp
>make
>make install

 - RabbitMQ 설치
>cd ~/apps
>wget http://10.114.81.109/install/asp-rabbit/rabbitmq-server-generic-unix-3.4.4.tar.gz
>tar -xvf rabbitmq-server-generic-unix-*.tar.gz
>ln -s rabbitmq_server-3.4.4 rabbitmq_server

 - path 설정
>cat >> ~/.bashrc
>export PATH=/home1/irteam/apps/otp/bin:/home1/irteam/apps/rabbitmq_server/sbin/:$PATH
>source ~/.bashrc

 - 환경설정(rabbitmq_server/etc/rabbitmq/rabbitmq-env.conf)
>RABBITMQ_LOG_BASE=/home1/irteam/logs/rabbitmq 
>RABBITMQ_NODENAME=test / ASP-KR 
>RABBITMQ_NODE_PORT=5672 

>echo -n "aaaa" > ~/.erlang.cookie 
>chmod 400 ~/.erlang.cookie 

>rabbitmqctl stop -n rabbit 
>rabbitmq-server -detached 

 - 모니터링 툴 설치
>rabbitmq-plugins enable rabbitmq_management
>rabbitmq-plugins enable rabbitmq_management_visualiser
>rabbitmq-plugins enable rabbitmq_management_agent

 - 클러스터 구성
   - A,B RabbitMQ 서버 시작
  > /home1/irteam/apps/rabbitmq_server/sbin/rabbitmq-server -detached
   
   - B에서 Cluster Join
  > rabbitmqctl stop_app
  > rabbitmqctl join_cluster test@dev-rabbitmq001
  > rabbitmqctl start_app
  > rabbitmqctl cluster_status
   
   - management_agent 설치필요
  > rabbitmq-plugins enable rabbitmq_management_agent
   
- 클러스터 추가 설정
 > - 클러스터의 경우 disc / ram 두가지 모드를 사용할수 있는데 1개 이상의 disc 설정을 권장.but. 우리는 좀 더 안정적으로 동작해야할 필요가 있기 때문에 두대를 disc로 설정하고 나머지 서버에서 ram으로 변경
  

## 코드구성
###Controller
> 1) FileUploadController.java
  >- String getImage(@RequestParam("uri") String uri)
  >- String uploadFileHandler(UploadRequest upReq)
  >- String checkThumbnail( @RequestParam("flowFileName") String name)
  
### Service
1) FileInformService.java
```
  /**
	 * Pre		: Receive periodic request message from client
	 * Func		: Check and return the state of header validation check
	 * Post		: -1 : stop upload
	 * 			: else : change nothing
	 */
- boolean checkHeaderValidation(String fileName)
```
```
 /**
	 * Pre		: Received invalidate result of header
	 * Func		: Make invalidate header message as a json format
	 * Post		: Client will stop upload
	 */
- JSONObject getHeaderInvalidMsg()
```
```
  /**
	 * Pre		: Receive periodic request message from client
	 * Func		: Check the upload request file is already resist
	 * Post		: Resist request file or use already uploaded data
	 */
-  boolean isRegist(String fileName)
```
```
  /**
	 * Pre		: Request image use URI from client
	 * Func		: Return image data according to URI
	 * Post		: Update the UI on browser
	 */
- String getImage(String uri)
```
```
  /**
	 * Pre		: Receive periodic request message from client
	 * Func		: Check not updated images those already extracted
	 * Post		: URI Request from client
	 */
- boolean isUpdated(String fileName)
```
```
  /**
	 * Pre		: Check is any updated URI on server
	 * Func		: Push thumbnail URIs to the URI request queue
	 * Post		: Response to client using URI request queue
	 */
- JSONObject getUpdatedURI(String fileName)
```
```
  /**
	 * Pre		: Receive periodic request message from client
	 * 			: Fail to write on the dst file
	 * Func		: Check the retry message for uploading again
	 * Post		: Send the retry upload list to client
	 */
- boolean isRetry(String fileName)
```
```
  /**
	 * Pre		: Check how many upload retry chunks exist
	 * Func		: Make json message and return it
	 * Post		: Reupload the chunks 
	 */
- JSONObject getRetryList(String fileName, JSONObject obj)
```


2) ThumbnailService.java
```
/**
	 * Pre		: Exist any indexes in possible queue
	 * 			  and already received the result of header validation(Duration)
	 * Func		: Send header validation check message to the RabbitMQ server
	 * Post		: Receive thumbnail uri and push it uri request list
	 * 			  or receive retry request and push it possible queue again
	 */
- void sendToRabbitMq(String queName, String message)
```
```
/**
	 * Pre		: Receive thumbnail URI from rabbitMQ
	 * Func		: Push the received URI to the URI request queue
	 * Post		: Poll the URI when client request
	 */
- void addThumbanilURI(String fileName, String thumbnailName)
```
```
/**
	 * Pre		: Receive the result of header validation check
	 * Func		: Parse the result as integer type
	 * Post		: Set the thumbnail request time
	 * 
	 * @return integer type duration or -1
	 */
- int parseDuration(String strDuration)
```
```
/**
	 * Pre		: Receive the result of header validation check
	 * Func		: Set duration and thumbnail extraction command accroding to duration
	 * Post		: Request thumbnail extract via rabbitMQ
	 */
- void setDuration(String fileName, String strDuration)
```
```
/**
	 * Pre		: Receive the result of thumbnail extraction from ffmpeg server
	 * Func		: Push the retry request to the possible queue
	 * Post		: Send this message again to the ffmpeg server
	 */
- void retryExtractThumbnail(String fileName, int index)
```
```
/**
	 * Pre		: Checked the upload progress and push thumbnail index data to the possible queue
	 * Func		: Make a json format message according to data in possible queue
	 * 			  and send data to the ffmpeg server via rabbitMQ
	 * Post		: Receive thumbnail uri
	 * @param possibleSize
	 * @param fileName
	 */
- thumbnailExtract(int possibleSize, String fileName)
```
```
/**
	 * Pre		: Check upload progress and return index(how many indexes are possible)
	 * Func		: Move index from ready to possible queue according to index
	 * Post		: Send thumbnail extraction message using these data
	 */
- void moveThumbnailQueue(String fileName, int index)
```
```
/**
	 * Pre		: Receive the result of header validation check
	 * Func		: Change integer data to string format
	 * Post		: Use the parameter of thumbnail extraction request
	 */
- String secondToHMS(int sec)
```

3) VideoUploadService.java
```
	/**
	 * Pre		: Already received first and last chunks
	 * Func		: Send header validation check message to the RabbitMQ server
	 * Post		: Wait the result of header validation as asynchronously
	 * 
	 * @param queNqme : "HEADER_VALIDATION"
	 */
- void sendToRabbitMq(String queName, String message)
```
```
/**
	 * Pre		: Get upload request from client
	 * Func		: Make basic video and thumbnail data set
	 * Post		: Receive more chunks and fill the data set
	 */
- void firstUpload(String fileName, int chunkNumber, long srcFileSize, int flowTotalChunks)
```
```
/**
	 * Pre		: Get upload request from client
	 * Func		: Write received chunk to the dst file
	 * 			: Check the upload progress and execute extra service according to progress
	 * Post		: Upload complete and send thumbnail extract request
	 */
- @Async void upload(UploadRequest upReq, byte[] tmpBytes )
 ```
 ```
/**
	 * Pre		: Already received first and last chunks
	 * Func		: Make header validation request as json format
	 * 			: and send to the rabbitmq server
	 * Post		: Wait the result of header validation check
	 */
- void headerCheckMsg(String fileName)
```
```
/**
	 * Pre		: Receive data chunks from client
	 * Func		: Check how many chunks are received according to thumbnail index(x% ~ (x+15)%)
	 * Post		: Send indexes from ready queue to possible queue
	 * @return index : The count of chunks for send to possible queue
	 */
- int checkProgress(String fileName, int currentRcv, int flowTotalChunks)
```

### Listener
1) HeaderValidationListener.java
```
/**
	 * Pre		: Pull message from rabbitMQ
	 * Func		: Process message
	 * Post		: Process according to header validation 
	 */
- void onMessage(Message message)
```
```
/**
	 * Pre		: Pull message from rabbitMQ
	 * Func		: Parse data from message and set duration
	 * Post		: Set thumbnail request data or stop to upload
	 */
- void messageProcess(String queName, String message)
```
```
/**
	 * Pre		: Pull message from rabbitMQ
	 * Func		: Parse value from message using key
	 * Post		: Check the header validation
	 */
- String jsonParse(String msg, String key)
```

2) ThumbnailServiceListener.java
```
	/**
	 * Pre		: Pull message from rabbitMQ
	 * Func		: Process message
	 * Post		: Process according to header validation 
	 */
- void onMessage(Message message)
```
```
/**
	 * Pre		: Pull message from rabbitMQ
	 * Func		: Check the return message is error or not
	 * Post		: Push URI to the URI list(success)
	 * 		Push message to the possible queue(fail)
	 */
- void messageProcess(String queName, String message)
```
```
/**
	 * Pre		: Pull message from rabbitMQ
	 * Func		: Parse value from message using key
	 * Post		: Check the header validation
	 */
- String jsonParse(String msg, String key) 
```
### Dao
1) ThumbnailDao.java 
2) VidoDao.java 

### Model
1) Thumbnail.java 
2) Video.java 
3) UploadRequest.java 


## 실행방법
 1) RabbitMQ (irteam@dev-uploader001)
  - Dir : /home1/irteam/apps/rabbitmq_server_sbin/
  - rabbitmq-server 명령 실행
  
 2) WAS (irteam@dev-ffmpeg001)
  - Dir : /home1/irteam/apps/tomcat/conf/server.xml
  - 아래의 코드 추가
 ``` <Context path="/thumbnail_Write" debug="0" privileged="true" docBase="thumbnail_Write.war" /> ```

  - Dir : /home1/irteam/apps/tomcat/webapps
  - 아래의 경로에 thumbnail_Write.war 추가
  
  - startup.sh 명령 수행. 
 
 3) ffmpeg 수행 프로그램(irteam@dev-ffmpeg001)
  - Dir : /home1/irteam/
  - 위의 경로에 ffmpeg.jar 추가
  - java -jar ffmpeg.jar 명령 수행.

## 실행 및 결과
 1) 실행
  - URL: 10.101.52.51:8080/thumbnail_Write/upload.jsp
  
 2) 결과
  - 결과 저장 서버 : irteam@dev-ffmpeg001
  - 결과 저장 경로 : /home1/irteam/apps/tomcat/tmpFiles
  - 메시지 큐 모니터링 : http://10.101.25.191:15672/
