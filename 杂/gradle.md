# gradle 
- 构建工具


```gradle

import com.sun.org.apache.xalan.internal.xsltc.cmdline.Compile

plugins {
    id 'java'
    id 'war'

}

group 'top.ccxh'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenLocal()
    mavenCentral()


}

/*版本冲突 直接编译失败*/
configurations.all {
    resolutionStrategy{
        failOnVersionConflict()
        //指定版本号
     //   force
    }
}
dependencies {
    compile ('org.hibernate:hibernate-core:3.6.3.Final') {
        //排包
        exclude group:'org.slf4j',module:'slf4j-api' 
    }
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile 'ch.qos.logback:logback-access:1.1.2'
    //runtime
    //testRuntime
}


def createDir={
    path ->
        File dir =new File(path)
        println path
        if (!dir.exists()){
            dir.mkdirs();
        }
}

task markeJavaDir(){
    def paths =['src/main/java',
                'src/main/resources','src/test','src/test/java','src/test/resources']
    doFirst {
        paths.forEach(createDir);
    }
}

task mkdirJavaWevDir(){
    dependsOn markeJavaDir
    def paths =["src/webapp"]
    doFirst {
        paths.forEach(createDir);
    }
}
allprojects {
    //应用所有项目
}
subprojects {
    //应用于子项目
}
````