---
title: cocoapod私有库
date: 2017-05-31 17:00
---

CocoaPods是开发中大家很熟悉的第三方库管理工具，通过它我们可以很方便的对绝大部分第三方库添加删除或更新，另外一方面，在项目开发过程中，会有一些可以复用的代码包或工具类，可是由于企业或自身原因不能对外公开，这时候通过CocoaPods的另一个功能--私有库来管理就很有必要了。

创建私有库需要准备两个仓库，一个用来管理代码，一个用来管理私有库信息。

下面我们开始分别创建它们！

<!--more-->

### 一、创建代码库（projectKit）

使用pods命令创建代码库。

1. 在GitLab上新建项目projectKit(仓库地址为 ``git@192.168.250.71:ios/projectKit.git`` )
2. 在要创建工程的目录下，通过pods命令``pod lib create projectName`` ，创建本地工程，控制台会输出：

```
pod lib create projectKit

Cloning `https://github.com/CocoaPods/pod-template.git` into `projectKit`.
Configuring projectKit template.
security: SecKeychainSearchCopyNext: The specified item could not be found in the keychain.

------------------------------

To get you started we need to ask a few questions, this should only take a minute.

If this is your first time we recommend running through with the guide:
 - http://guides.cocoapods.org/making/using-pod-lib-create.html
 ( hold cmd and double click links to open in a browser. )
```

然后有四个问题需要回答（根据自己需要选择）：

```question
What language do you want to use?? [ Swift / ObjC ] //构建项目语言
ObjC
Would you like to include a demo application with your library? [ Yes / No ] //是否需要demo
No
Which testing frameworks will you use? [ Specta / Kiwi / None ] //选择一个测试框架
None
Would you like to do view based testing? [ Yes / No ] //是否基于view测试
No
What is your class prefix? //项目文件前缀
Shareit
```

回车，创建成功后terminal的提示是

```create successed
Ace! you're ready to go!

 We will start you off by opening your project in Xcode

  open 'projectKit/Example/projectKit.xcworkspace'

To learn more about the template see https://github.com/CocoaPods/pod-template.git.

To learn more about creating a new pod, see http://guides.cocoapods.org/making/making-a-cocoapod.
```

创建好的工程目录结构如下：

```
.
├── Example
│   ├── Podfile
│   ├── Podfile.lock
│   ├── Pods
│   │   ├── Headers
│   │   ├── Local\ Podspecs
│   │   │   └── projectKit.podspec.json
│   │   ├── Manifest.lock
│   │   ├── Pods.xcodeproj
│   │   │   ├── project.pbxproj
│   │   │   └── xcuserdata
│   │   └── Target\ Support\ Files
│   │       ├── Pods-projectKit_Tests
│   │       └── projectKit
│   ├── Tests
│   │   ├── Tests-Info.plist
│   │   ├── Tests-Prefix.pch
│   │   ├── Tests.m
│   │   └── en.lproj
│   │       └── InfoPlist.strings
│   ├── projectKit.xcodeproj
│   │   ├── project.pbxproj
│   │   ├── project.xcworkspace
│   │   │   └── contents.xcworkspacedata
│   │   ├── xcshareddata
│   │   │   └── xcschemes
│   │   └── xcuserdata
│   │       └── may.xcuserdatad
│   └── projectKit.xcworkspace
│       ├── contents.xcworkspacedata
│       └── xcuserdata
│           └── may.xcuserdatad
├── LICENSE
├── README.md
├── _Pods.xcodeproj -> Example/Pods/Pods.xcodeproj
├── projectKit
│   ├── Assets
│   └── Classes
│       └── ReplaceMe.m // 这里要替换成私有库要存放的代码文件
└── projectKit.podspec // 要提交到另一个仓库的文件
```

#### (一)、修改.podspec文件，给私有库添加文件

在该工程目录下可以看到.podspec文件，文件名与该工程名一致，这个文件是对代码库的版本信息，地址等进行管理的，我们需要对他进行一些参数的修改，打开后根据需要修改参数(修改参数需要注意，会影响之后的验证，稍不留神验证不过去了，具体参数含义参考[sepc文件官方规范文档](https://guides.cocoapods.org/syntax/podspec.html))

```
s.name  =  'projectKit' // name必须和podspec文件名一致
s.author = { 'caoml' => 'caoml@ushareit,com' } 或  'caoml'  或  'caoml','may'   或   { 'caoml' => 'caoml@ushareit,com', 'may' => 'may@ushareit,com' }

s.version          = '0.1.0' // 提交代码后，注意添加与该参数一致的标签

s.summary = 'projectKit is a testKit.'  // 注意语法，大小写，标点符号

s.homepage = 'http://www.ushareit.com' // 公司内部必须为该网址

s.source  = { :git => 'git@192.168.250.71:ios/projectKit.git', :tag => s.version.to_s }  // 代码库地址
#s.license  = { :type => 'MIT', :file => 'LICENSE' }  // 不用写，注释掉

s.description      = <<-DESC
welocme to projectKit   // 必须遵循这个格式，不能合成一行或两行
                       DESC                                              

s.source_files = 'projectKit/Classes/*.{h,m}'  // 路径是从与.podspec文件平级开始的

s.public_header_files = 'Pod/Classes/***/.h'  //公开头文件地址修改好podspec后使用命令
```

在工目录``~/projectKit/projectKit/Classes/``下添加文件``ProjectView.h,porjectView.m``（可以新建，也可以直接从别的项目里复制过来，但是要保证是可运行的），现在目录结构如下：

```
.
├── Example
│   ├── Podfile
│   ├── Podfile.lock
│   ├── Pods
│   │   ├── Headers
│   │   ├── Local\ Podspecs
│   │   │   └── projectKit.podspec.json
│   │   ├── Manifest.lock
│   │   ├── Pods.xcodeproj
│   │   │   ├── project.pbxproj
│   │   │   └── xcuserdata
│   │   └── Target\ Support\ Files
│   │       ├── Pods-projectKit_Tests
│   │       └── projectKit
│   ├── Tests
│   │   ├── Tests-Info.plist
│   │   ├── Tests-Prefix.pch
│   │   ├── Tests.m
│   │   └── en.lproj
│   │       └── InfoPlist.strings
│   ├── projectKit.xcodeproj
│   │   ├── project.pbxproj
│   │   ├── project.xcworkspace
│   │   │   └── contents.xcworkspacedata
│   │   ├── xcshareddata
│   │   │   └── xcschemes
│   │   └── xcuserdata
│   │       └── may.xcuserdatad
│   └── projectKit.xcworkspace
│       ├── contents.xcworkspacedata
│       └── xcuserdata
│           └── may.xcuserdatad
├── LICENSE
├── README.md
├── _Pods.xcodeproj -> Example/Pods/Pods.xcodeproj
├── projectKit
│   ├── Assets
│   └── Classes
│       ├── ProjectView.h //添加的私有库文件
│       └── ProjectView.m
└── projectKit.podspec
```

#### (二)、验证.podspec文件

.podspec文件修改完成后为了保证提交后是可用的，我们需要对它进行验证，使用命令``pod lib lint ``验证，如有warnings 命令后加--allow-warnings可以忽略warnings，终端输出为：

```
pod lib lint --allow-warnings

 -> projectKit (0.1.0)
    - WARN  | attributes: Missing required attribute `license`.
    - WARN  | license: Missing license type.
    - WARN  | source: Git SSH URLs will NOT work for people behind firewalls configured to only allow HTTP, therefore HTTPS is preferred.
    - WARN  | description: The description is shorter than the summary.
    - WARN  | url: There was a problem validating the URL www.ushareit.com.

projectKit passed validation.
```

``projectKit passed validation.``表示验证通过。

将本地代码库提交到 projectKit (git@192.168.250.71:ios/projectKit.git) ，**给本次提交添加与s.version参数一致的标签**。

事实上，如果我们做到这一步，私有库是完全可以用的，可是在真正的开发中，往往是多人协作的，要想参与人员都能够使用它则必须创建另一个库来管理podspec文件。

### 二、创建spec私有库



1. 在GitLab上新建仓库cocoapods (仓库地址为git@192.168.250.71:ios/cocoapods.git)。

2. 执行命令 `pod repo add podName  specGitAddress` ，把私有库名称和podsepc管理库关联起来：

   `pod repo add projectPods git@192.168.250.71:ios/cocoapods.git`

3. 在``~/.cocoapods/repos``目录下可以看到大概目录结构如下（该目录是隐藏文件，显示隐藏文件命令在文章末尾）：

   ```repos
   .
   ├── master
   │   ├── CocoaPods-version.yml
   │   ├── README.md
   │   └── Specs
   └── projectPods
       └── README.md
   ```

   目录结构中，master是CocoaPods官方spec管理库，在master目录下的repos文件夹包括所有公开第三方库各个版本的podspec文件。

4. 使用命令 (``pod repo push (私有库名)  (spec文件名)``)，把代码库中的podspec文件提交到spec管理库中：

``pod repo push projectPods projectKit.podspec --allow-warnings ``

提交成功后在~/.cocoapods/repo／projectPods目录下就可以看到我们的projectKit私有库了。目录结构如下:

```
.
├── README.md
└── projectKit
    └── 0.3.0 // .podspec文件中的版本号
        └── projectKit.podspec
```

此时在远端git上也会有一次提交，这样我们的私有库就创建好了。

**tip**: 如果在pods目录下没有看到master，执行pod setup命令成功后可以看到。

### 三、私有库的使用

打开一个包含CocoaPods的工程，与引用其他第三方库一样修改podfile 文件，同时因为是私有库，我们需要把spec管理库地址写上去，修改如下：

```
source 'https://github.com/CocoaPods/Specs.git'  // 官方spec管理库地址
source ‘git@192.168.250.71:ios/cocoapods.git’ // 私有spec管理库地址
target "uShareit" do
    pod 'MBProgressHUD'
    pod 'Alamofire'
	pod 'projectKit', '~> 0.3.0'
end
```

然后执行pod update更新依赖库， 就可以看到我们私有库的库文件在pods的子文件夹中了。

综上，我们的私有库就创建完成了！

### 四、私有库的升级

再私有库的使用过程中，不可避免的我们需要对它进行修改升级，接下来解释一下如何对私有库进行升级使用。

1. 私有库的版本控制有两个重要参数，一个是.podspec文件中的**`s.version`**，另一个是代码库提交时添加的标签；

2. 在对代码库进行修改后，一定要同时修改.podspec文件的**`s.version`**,提交代码到本地后，给本次提交添加与**`s.version`**同样的值；

3. 在代码库根目录下，再次执行命令``pod repo push (私有库名)  (spec文件名)``，更新spec管理库：

   ``pod repo push projectPods projectKit.podspec --allow-warnings ``      

4. 再次查看``~/.cocoapods/repos``目录：

       .
       └── repos
       ├── master
       │   ├── CocoaPods-version.yml
       │   ├── README.md
       │   └── Specs  
       └── projectPods
           ├── README.md
           └── projectKit
               ├── 0.3.0
       │       └── 0.4.0

   我们发现，projectKit库包含了两个文件夹，分别对应的是两次提交的版本，每个文件夹下都是对应版本的.podspec文件，使用时，只要在``pod 'projectKit', '~> 0.4.0'``后添加对应的版本号就可以拿到不同版本的代码了，至此，我们对私有库的升级就完成了。


**tip1**:如果在`pod lib lint`或`pod repo push (私有库名)  (spec文件名)`时发生以下错误，在使用命令时加上`--user-libraries`：

```
error:'The 'Pods' target has  transitive dependencies
pob lib lint --use-libraries --allow-warnings
```

**tip2**:podspec文件还有一个参数是 ` weak_frameworks` ，表示弱链接，比如说一个项目同时兼容iOS6和iOS7，但某一个framework只在iOS7上有，这时候如果用强链接，那么在iOS7上运行就会crash，使用weak_frameworks可以避免这种情况。用法与frameworks一样。

### 五、私有库引用其他第三方库

#### (一)、引用静态库和framework

1. 将要引用的第三方库(这里包括静态库和.framework)添加到代码库`~/projectKit/projectKit/Classes`目录下，目录结构为：

   ```
   .
   ├── Example
   │   ├── Podfile
   │   ├── Podfile.lock
   │   ├── Pods
   │   │   ├── Local\ Podspecs
   │   │   │   └── projectKit.podspec.json
   │   │   ├── Manifest.lock
   │   │   ├── Pods.xcodeproj
   │   │   │   ├── project.pbxproj
   │   │   │   └── xcuserdata
   │   │   ├── Target\ Support\ Files
   │   │   │   ├── Pods-projectKit_Tests
   │   │   │   └── projectKit
   │   │   └── projectKit
   │   │       ├── LICENSE
   │   │       ├── README.md
   │   │       └── projectKit
   │   ├── Tests
   │   │   ├── Tests-Info.plist
   │   │   ├── Tests-Prefix.pch
   │   │   ├── Tests.m
   │   │   └── en.lproj
   │   │       └── InfoPlist.strings
   │   ├── projectKit.xcodeproj
   │   │   ├── project.pbxproj
   │   │   ├── project.xcworkspace
   │   │   │   └── contents.xcworkspacedata
   │   │   ├── xcshareddata
   │   │   │   └── xcschemes
   │   │   └── xcuserdata
   │   │       └── may.xcuserdatad
   │   └── projectKit.xcworkspace
   │       ├── contents.xcworkspacedata
   │       └── xcuserdata
   │           └── may.xcuserdatad
   ├── LICENSE
   ├── README.md
   ├── _Pods.xcodeproj -> Example/Pods/Pods.xcodeproj
   ├── projectKit
   │   ├── Crashlytics
   │       │   ├── Crashlytic.framework
   │       │   │   ├── README
   │       │   │   └── submit
   │       │   ├── README.md
   │       │   ├── iOS
   │       │   │   └── Crashlytics.framework // 添加的framework
   │       │   └── submit
   │       └── libmp3lame.a  // 添加的静态库文件
   └── projectKit.podspec
   ```

2. 如下修改.podspec文件：

   ```
   s.version = '0.5.0'
   s.ios.vendored_libraries = 'projectKit/Classes/**/libmp3lame.a' // 静态库文件
   s.frameworks = 'UIKit', 'MapKit' // 多个系统framework
   s.ios.vendored_frameworks = 'projectKit/Classes/**/Crashlytics.framework' // 第三方framework
   ```

   提交代码修改到本地，并给此次提交添加标签为0.5.0，然后将修改提交到远端仓库；

3. 执行命令``pod repo push (私有库名)  (spec文件名)``，更新远端spec管理库：

   ``pod repo push projectPods projectKit.podspec --allow-warnings ``      

4. 到使用CocoaPods项目中修改podfile文件，然后在该项目目录下执行`pod update`，成功后就可以在pods子文件夹下看到静态库和framework文件了

   ```
   source 'https://github.com/CocoaPods/Specs.git'  // 官方spec管理库地址
   source ‘git@192.168.250.71:ios/cocoapods.git’ // 私有spec管理库地址
   target "uShareit" do
       pod 'MBProgressHUD'
       pod 'Alamofire'
   	pod 'projectKit', '~> 0.5.0'
   end
   ```

#### (二)、私有库引用pods库

pods库分两种，一种是pods官方包括的第三方库，另一种是没有公开的私有库。

##### 引用官方第三方库

1. 私有库要引用pods官方的第三方库只需要在.podspec文件中添加以下参数：

   ```
   s.dependency 'AFNetworking', '~>3.0'
   s.dependency 'JSONKit', '~>2.0'
   ```

2. 修改s.version，提交代码修改到本地，并给此次提交添加标签，然后将修改提交到远端仓库；

3. 执行命令``pod repo push (私有库名)  (spec文件名)``，更新远端spec管理库：
   ``pod repo push projectPods projectKit.podspec --allow-warnings ``   

4. 到工程目录下修改podfile文件的私有库版本号，并执行pod update,成功后在pods子目录下可以看到`AFNetworking`和`JSONKit`。

##### 引用私有库

1. 私有库a引用私有库b时，同样需要在.podspec文件中添加参数：

   ```
     s.dependency 'projectKit2', '~>0.1.0' // 这里我的另一个私有库名字是projectKit2
   ```

   **tip**:如果要对spec文件进行验证，则需要在执行`　pod spec lint`命令时在后边接上私有库的资源地址，否则pods将会从cocoapods官方查询。

   ```
   pod spec lint --sources=git@192.168.250.71:ios/cocoapods2.git,master --allow-warnings // source后是projectKit2的spec管理库地址和分支名
   ```

2. 修改s.version，提交代码修改到本地，并给此次提交添加标签，然后将修改提交到远端仓库；

3. 执行更新远端spec 管理库命令时同样需要在命令后加上私有库b的资源地址：

   ```
   pod repo push projectPods projectKit.podspec --sources=git@192.168.250.71:ios/cocoapods2.git,master --allow-warnings
   ```

4. 到工程目录下修改podfile文件，执行pod update,成功后在pods子目录下可以看到`projectKit2`:

   ```
   source ‘git@192.168.250.71:ios/cocoapods.git’ // 私有库a spec管理库地址
   source ‘git@192.168.250.71:ios/cocoapods2.git’ // a引用的私有库b spec管理库地址
   source 'https://github.com/CocoaPods/Specs.git' // 官方spec管理库地址
   platform :ios, '9.0'
   target 'podsTest' do
     # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
     #use_frameworks!
     pod 'projectKit', '~> 1.0.3’ // 引用私有库b的私有库a名称和版本号
   end
   ```



**tip1**:在对私有库升级时，如果s.version和提交时标签不一致，使用时获取到的代码与s.version的版本号一致。

**tip2**:pod repo list 查看当前库源

**tip3**:显示隐藏文件命令：

`defaults write com.apple.finder AppleShowAllFiles -bool true`

关闭隐藏文件命令：

`defaults write com.apple.finder AppleShowAllFiles -bool false`。

**tip4**: 自己创建podspec文件命令:

``pod spec create podspec名 代码库地址``
