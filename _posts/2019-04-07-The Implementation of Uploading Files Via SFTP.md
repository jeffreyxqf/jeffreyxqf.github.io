---

layout: post
title: 2019-04-07-The Implementation of Uploading Files Via SFTP
tags: Java 
category: 
comments: true

---

项目中需要上传文件至远程服务器，本文主要介绍了通过单例方式实现SFTP协议，达到同步文件至远程Sever的目的。








```java
public class SFTPClient {

    private  String host;

    private  int port;

    private  String userName;

    private  String password;

    private  int timeout;


    private static ChannelSftp sftp;

    private static SFTPClient instance = null;

    private SFTPClient() {
    }

    public static SFTPClient getInstance() {
        if (instance == null) {
            if (instance == null) {
                instance = new SFTPClient();
                sftp = instance.connect(host, port, userName, password);
            }
        }
        return instance;
    }

    /**
     * connect
     *
     * @return
     */
    public ChannelSftp connect(String host, int port, String username, String password) {
        ChannelSftp sftp = null;
        try {
            JSch jsch = new JSch();
            jsch.getSession(username, host, port);
            Session sshSession = jsch.getSession(username, host, port);
            sshSession.setPassword(password);
            Properties sshConfig = new Properties();
            sshConfig.put("StrictHostKeyChecking", "no");
            sshSession.setConfig(sshConfig);
            sshSession.connect();
            logger.info("SFTP Session connected.");
            Channel channel = sshSession.openChannel("sftp");
            channel.connect();
            sftp = (ChannelSftp) channel;
            logger.info("Connected to " + host);
        } catch (Exception e) {
            logger.error(e.getMessage());
            e.printStackTrace();
        }
        return sftp;
    }

    /**
     * 上传文件
     *
     * @param dst        上传的目录
     * @param uploadFile 要上传的文件
     */
    public boolean upload(String dst, String uploadFile) {
        try {
            sftp.cd(dst);
            File file = new File(uploadFile);
            FileInputStream fileInputStream = new FileInputStream(file);
            sftp.put(fileInputStream, file.getName());
            fileInputStream.close();
            return true;
        } catch (Exception e) {
            logger.error(e.getMessage());
            e.printStackTrace();
            return false;
        } finally {
            File file = new File(uploadFile);
            if (file.exists() && file.isFile()) {
                file.delete();
            }
            sftp.disconnect();
        }
    }

    public String getHost() {
        return host;
    }

    public void setHost(String host) {
        this.host = host;
    }

    public int getPort() {
        return port;
    }

    public void setPort(int port) {
        this.port = port;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public int getTimeout() {
        return timeout;
    }

    public void setTimeout(int timeout) {
        this.timeout = timeout;
    }
}
```


