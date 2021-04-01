1. ##### 首先创建自己的骨架结构

2. ##### 进入当前maven所在目录 执行下边命令

   ```
   mvn archetype:create-from-project
   ```

   注：若报错，注意用户`.m2`目录下是否有`setting.xml`配置文件。

3. **进入到`target`目录执行指令**

   ```
   cd target\generated-sources\archetype
   mvn clean install
   ```

4. **在新建项目时创建Archetype**

   注：填写项目坐标时使用的是`target\generated-sources\archetype`目录下的`pom`文件中的坐标