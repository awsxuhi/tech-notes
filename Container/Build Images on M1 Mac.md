Build Docker Images on M1 series chips in Mac



修改Dockerfile文件，在第一行使用`--platform=linux/amd64`来拉取镜像，这样可以保证在Mac M1芯片上使用docker desktop构建的镜像可以在使用intel X86架构的ECS上正常运行。