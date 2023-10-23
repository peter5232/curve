FROM opencurvedocker/curve-base:build-openeuler AS curve-sdk
ENV GITHUB_PROXY=https://ghproxy.com/ 

#RUN git clone https://github.com/opencurve/curve && \
#     cd /curve && git checkout a9db5df70898dad53c76369ed0f732477f3e4c4f  \
#     && cp thirdparties/brpc/fix-gcc11.patch / \
#     && git checkout a14bcd3ecdfcedd9398cbe74edf17b8173e417a0  \
#     && mv /fix-gcc11.patch thirdparties/brpc/fix-gcc11.patch  \
#     &&  sed -i "s;//:thirdparties/brpc/brpc.patch;//:thirdparties/brpc/brpc.patch\",\"//:thirdparties/brpc/fix-gcc11.patch;g" WORKSPACE 
RUN git clone https://ghproxy.com/https://github.com/peter5232/curve.git
RUN cd /curve && make dep stor=bs os=openeuler
RUN cd /curve && make dep stor=fs os=openeuler
#RUN cd /curve && bash util/build_in_image.sh --stor=bs '--only=src/*' --dep=1 --release=1 --ci=0 --os=openeuler
#RUN cd  /curve &&  bash util/build_in_image.sh --stor=fs '--only=src/*' --dep=1 --release=1 --ci=0 --os=openeuler

# 设置pip镜像
RUN echo -e "[global]\nindex-url = https://pypi.tuna.tsinghua.edu.cn/simple" >>/root/.config/pip/pip.conf
RUN wget https://curve-build.nos-eastchina1.126.net/get-pip.py && python2 get-pip.py && python2 -m pip install wheel setuptools

RUN cd /curve &&  bash -x mk-tar.sh
RUN cd /curve &&  mv curve_*.tar.gz curve_sdk.tar.gz
RUN cd /curve && ls ./curvefs_python/tmplib/


FROM opencurvedocker/curve-base:build-openeuler AS curve-tgt
ENV GITHUB_PROXY=https://ghproxy.com/
COPY --from=curve-sdk /curve/curve_sdk.tar.gz /
RUN tar -zxvf curve_sdk.tar.gz && \
     rm curve_sdk.tar.gz && \
    cd /curve/curve-sdk && \
    cp -f lib/* /usr/lib && \
    cp -f bin/* /usr/bin && \
    mkdir -p /usr/curvefs && \
    cp -f curvefs/* /usr/curvefs && \
    cp -f include/* /usr/include && \
    cd / && \
    git clone --branch curve ${GITHUB_PROXY}https://github.com/opencurve/curve-tgt && \
    cd curve-tgt && \
    make programs
