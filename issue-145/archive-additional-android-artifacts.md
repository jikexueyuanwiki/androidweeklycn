# 附加Android工件和Gradle的档案 

当构建 Android 应用程序或库时，常见的做法是将你的工件保存到本地文件储存或回购。 

除了你的 APK，还有一些你想要/需要保存的附加的工件，并且你想要这样做。最常见的一个是 Javadoc，或许是你的 [proguard](http://proguard.sourceforge.net/) 生成文件，如映射文件。 

我当然希望 Gradle 任务能够处理这个问题。所以让我们看看你可能想用的一些任务。 

## 添加Javadoc归档任务 

下面将添加任务来为每一个构建类型生成 Javadocs，并组装成 jar 存档。

```
    android.applicationVariants.all { variant ->
    	project.task("${variant.name.capitalize()}Javadoc", type: Javadoc) {

        	destinationDir = new File("$project.buildDir/javadoc/$variant.name")

        	source = variant.javaCompile.source

        	ext.androidJar = "${project.android.sdkDirectory}/platforms/${project.android.compileSdkVersion}/android.jar"
        	classpath = project.files(variant.javaCompile.classpath.files) + project.files(ext.androidJar)

        	options {
            	linksOffline("http://d.android.com/reference", "${project.android.sdkDirectory}/docs/reference")
            	links("http://docs.oracle.com/javase/7/docs/api/");
            	setMemberLevel(JavadocMemberLevel.PACKAGE)
            	docEncoding = 'UTF-8'
            	encoding = 'UTF-8'
            	charSet = 'UTF-8'
        	}

        	exclude '**/BuildConfig.java'
        	exclude '**/R.java'
    	}

    	project.task("generate${variant.name.capitalize()}JavadocJar", type: Jar, dependsOn: "${variant.name.capitalize()}Javadoc") {
        	classifier 'javadoc'

        	description = 'Assembles a jar archive containing the generated Javadoc API documentation of $variant.name.'

        	destinationDir = new File("$project.buildDir/libs/")

        	exclude '**/BuildConfig.class'
        	exclude '**/R.class'

        	from "$project.buildDir/javadoc/$variant.name"
    	}
    } 
```

当添加到 `build.gradle` 文件时，会从 `gradle generateReleaseSourcesJar` 命令行生成 Javadoc jar 档案。 

## 添加源存档任务 

以下将添加任务来组装一个包含 Java 源的 jar 档案。

```
    android.applicationVariants.all { variant ->
    	project.task("generate${variant.name.capitalize()}SourcesJar", type: Jar) {
        	classifier = 'sources'

        	description = 'Assembles a jar archive containing the main sources of $variant.name..'

       		destinationDir = new File("$project.buildDir/libs/")

        	// exclude generated files
        	exclude '**/BuildConfig.java'
        	exclude '**/R.java'

        	from variant.javaCompile.source
    	}
    } 
```

当添加到你的 `build.gradle` 文件时，会从 `gradle generateReleaseSourcesJar` 的命令行生成你的源 jar 档案。 

## 添加混淆器归档任务 

以下将添加任务来组装一个包含生成混淆文件的 zip 档案。

```
    android.applicationVariants.all { variant ->
    	project.task("generate${variant.name.capitalize()}ProguardFilesJar", type: Zip) {
        	classifier 'proguard'
        
        	description = 'Assembles a jar archive containing the Proguard files of $variant.name..'
        
        	destinationDir = new File("$project.buildDir/libs/")
        	from "$project.buildDir/outputs/mapping"
    	}     
    }
```

当添加到你的 `build.gradle` 文件时，会从 `gradle generateReleaseProguardFilesJar` 的命令行生成混淆 zip 档案。
