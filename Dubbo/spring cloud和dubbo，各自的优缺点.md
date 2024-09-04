1. dubbo由于是二进制的传输，占用带宽会更少

2. springCloud是http协议传输，带宽会比较多，同时使用http协议一般会使用JSON报文，消耗会更大

3. dubbo的开发难度较大，原因是dubbo的jar包依赖问题很多大型工程无法解决

3. springcloud的接口协议约定比较自由且松散，需要有强有力的行政措施来限制接口无序升级

