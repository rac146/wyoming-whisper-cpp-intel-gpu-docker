ARG BUILD_FROM
FROM ${BUILD_FROM}

ARG WHISPER_CPP_VERSION

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

#Intel driver
RUN wget -qO - https://repositories.intel.com/gpu/intel-graphics.key | \
    gpg --dearmor --output /usr/share/keyrings/intel-graphics.gpg && \
    echo "deb [arch=amd64,i386 signed-by=/usr/share/keyrings/intel-graphics.gpg] https://repositories.intel.com/gpu/ubuntu jammy client" \
    | tee /etc/apt/sources.list.d/intel-gpu-jammy.list

RUN \
    apt-get update \
    && apt-get install -y --no-install-recommends \
        netcat-traditional \
        intel-level-zero-gpu \
        level-zero && \
        apt-get clean && \    
        rm -rf  /var/lib/apt/lists/*

RUN \
    git clone https://github.com/ggerganov/whisper.cpp.git whisper.cpp \
    && cd whisper.cpp \
    && git reset --hard v${WHISPER_CPP_VERSION} \
    && cmake -B build -DGGML_SYCL=ON -DCMAKE_C_COMPILER=icx -DCMAKE_CXX_COMPILER=icpx -DGGML_SYCL_F16=ON \
    && cmake --build build -j --config Release

WORKDIR /whisper.cpp
