---
layout: post
title:  Transfer Files Between Win & Linux
category: Technologies
---

- Windows ——> Linux
>    
    Command：pscp localfile rootuser@remoteip:/fileDirectory　　
>     
    Copy Dir： pscp -r -l root -pw root localDir  remoteip:/fileDirectory



- Linux ——> Windows
>       
     pscp -r rootuser@remoteip:/fileDirectory localDir
>    
     pscp -r -l root -pw root remoteip:/fileDirectory localDir
  
You can download pscp from here : 

  http://www.nber.org/pscp.html 
 
 
 For ant task , you can use scp , for example : 
 
 >    
      <target name="upload_linux">
	    	<scp file="${dist.dir}/${package.name}_${version}.tar.gz" sftp="true" trust="true" todir="username:password@IPaddress:/Test" />
      </target>
