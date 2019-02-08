# 导出网易云歌单为m3u

此此分支是GUI版本,正在开发中\
由于刚学Pyside2(Qt),所以是在边学边开发\
希望2.12号(开学)之前能搞定并且并入主分支

**_适用于Windows_**

#### 此程序可以分析网易云客户端的数据库文件\
#### 然后在程序所在目录导出播放列表为m3u格式

此程序需要网易云的配置文件处于默认目录\
可能将来会做手动选择网易云数据库文件目录的功能\
如果安装的时候没有动过配置文件目录就没事（和安装目录没有关系，即使安装在D盘也许也行）\

可以只导出某个用户创建的歌单\
也可以只导出自己创建的歌单或者只下载收藏的别人的歌单

## 没有第三方依赖库

## 使用方法

首先打开程序后会开始自动分析网易云客户端数据库文件

然后直到出现这段文字，就可以开始进行筛选了。

    请输入筛选模式，1代表按照用户id筛选，2代表按照是否是自己的歌单筛选:

分析完毕的播放列表可以按照两种模式进行筛选导出

### 1.按照用户id来筛选

打开脚本后输入1即是此模式

回车之后输入歌单创建者的网易云用户id即可只导出此用户创建的歌单

#### 特别的：在此模式下不输入任何用户id直接回车就是导出全部歌单

### 2.按照是否是自己创建的播放列表来进行筛选

打开脚本后输入2即是此模式

回车之后输入1就是导出自己创建的歌单

输入2就是导出收藏的别人创建的歌单

## TODO：

- [x] 基础的导出网易云歌单功能
- [x] 按照用户id筛选
- [x] 按照是否是自己创建的歌单筛选
- [ ] 简单的GUI
    - [ ] 自定义输出播放列表文件目录
    - [ ] 图形化选择输出的歌单
    - [ ] ...

## 以下是各个函数的说明:

### logger
仅仅是一个日志记录器而已\
因为还没有做其他功能，以后估计会加上输出时间和debug信息啥的

输入: str
效果：print出str的内容
    
### get_dir_of_db
获取数据库文件的路径，返回值为一个字典
 
输入: 无
 
返回值： 
 
如果成功获取数据库文件路径，就返回一个

    {'library.dat': ..., 'webdb.dat': ..., 'ok': True}
    
这样的字典

如果失败的话就返回

    {'ok': False}
    
### get_playlist
获取播放列表，返回值为一个字典

输入：

    webdb.dat的文件路径，可以通过上面一个函数获取

返回值：播放列表的字典，格式为：

    {pid:{'playlist_name': ...,
            'userid': ...,
            'username': ...,
            'songs': list()}, }
            
其中，pid是播放列表的id，songs的值是一个列表，里面是这个播放列表里面的所有歌的tid

### get_songs_dir
获取网易云的歌曲下载目录

因为其中可能混杂着其他目录，所以如果某个目录出现频率超过0.9就认定为是下载目录

输入：
    
    library.dat的路径，可以通过get_dir_of_db获取

返回值：windows路径字符串

### tid2dir_offline
转化tid为本地路径

输入:

    1.library.dat的路径
    2.webdb.dat的路径
    3.可以通过get_dir_of_db获取

返回值：

    {tid1:dir1,tid2:dir2}
    
这样形式的字典,其中tid为int，dir为str

### playlist_dict_to_m3u
转化指定播放列表为m3u内容

输入：
    
    1.playlist字典（通过get_playlist获取)
    2.tid对应的路径字典(通过tid2dir获取)

输出：{playlist_name:m3u_content}格式的字典\
playlist_name为播放列表的名字，m3u_content为播放列表内容

### playlist_fliter_as_subscribed
按照是否为收藏的歌单过滤播放列表id（pid）

输入：
    
    1.dict:playlist
    2.boolean:
        True：筛选用户收藏的歌单
        False：筛选用户创建的歌单
        
返回值：

以pid为元素的列表

### playlist_filter_as_userid
按照歌单所有者来过滤

输入：

    1.dict:playlist
    2.int：用户ID
    
返回值：

以pid为元素的列表

### save_m3u
保存m3u文件于当前目录，以后估计会加上自定义目录。

输入:

    1.str: encoding:文件编码
    2.dict: playlist_dict_to_m3u输出的字典
    
效果：把m3u文件保存到程序所在目录

### main:

1.获取数据库文件目录，分别储存于library_dat和webdb_dat

2.初始化播放列表和歌曲转换表，分别储存于playlists和songs

3.用户选择第一个模式

4.用户选择第二个模式

5.得到playlist_ids 即filter输出的筛选表列表

6.根据playlists，songs和filter获取m3u_content

7.保存m3u文件。