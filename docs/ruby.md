## ruby

## 包

[ruby-2.6.3.tar.gz](https://cache.ruby-lang.org/pub/ruby/2.6/ruby-2.6.3.tar.gz)

[zlib-1.2.11.tar.gz](http://www.zlib.net/zlib-1.2.11.tar.gz)

[openssl-1.1.0j.tar.gz](https://www.openssl.org/source/openssl-1.1.0j.tar.gz)



### zlib

```bash
#选择 zlib
tar xvzf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure --prefix=/usr/local/zlib
make && make install
cd ..
rm -rf zlib-1.2.11
```

## openssl

```bash
tar xzvf openssl-1.1.0j.tar.gz
cd openssl-1.1.0j
./config -fPIC --prefix=/usr/local/openssl enable-shared
./config -t
make && make install 
cd ..
rm -rf openssl-1.1.0j
```

## ruby

```bash

tar vxzf ruby-2.6.3.tar.gz

cd ruby-2.6.3

./configure

make && make install


# ext zlib

cd ext/zlib

ruby extconf.rb  --with-zlib-include=/usr/local/zlib/include/ --with-zlib-lib=/usr/local/zlib/lib

# 这里可能存在问题，需要配置到源码实际位置
export top_srcdir=/data/pack/ruby-2.6.3

make && make install


#ext openssl

cd ../openssl

ruby extconf.rb  --with-openssl-include=/usr/local/openssl/include/ --with-openssl-lib=/usr/local/openssl/lib


export top_srcdir=/data/pack/ruby-2.6.3

make && make install

 cd ../../..
rm -rf ruby-2.6.3


#测试

ruby -v

gem -v
```



 

