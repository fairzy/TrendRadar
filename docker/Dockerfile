FROM python:3.10-slim

WORKDIR /app

ENV DEBIAN_FRONTEND=noninteractive

# supercronic：使用仓库内置的二进制，避免构建时访问 GitHub
# 下载（x86_64）：https://github.com/aptible/supercronic/releases/download/v0.2.39/supercronic-linux-amd64
COPY docker/supercronic-linux-amd64 /usr/local/bin/supercronic
RUN set -ex && \
    echo "c98bbf82c5f648aaac8708c182cc83046fe48423  /usr/local/bin/supercronic" | sha1sum -c - && \
    chmod +x /usr/local/bin/supercronic && \
    /usr/local/bin/supercronic -version

RUN set -ex && \
    if [ -f /etc/apt/sources.list ]; then \
        sed -i \
            -e 's|http://deb.debian.org/debian|http://mirrors.cloud.tencent.com/debian|g' \
            -e 's|https://deb.debian.org/debian|http://mirrors.cloud.tencent.com/debian|g' \
            -e 's|http://deb.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            -e 's|https://deb.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            -e 's|http://security.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            -e 's|https://security.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            /etc/apt/sources.list; \
    fi && \
    if [ -f /etc/apt/sources.list.d/debian.sources ]; then \
        sed -i \
            -e 's|http://deb.debian.org/debian|http://mirrors.cloud.tencent.com/debian|g' \
            -e 's|https://deb.debian.org/debian|http://mirrors.cloud.tencent.com/debian|g' \
            -e 's|http://deb.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            -e 's|https://deb.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            -e 's|http://security.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            -e 's|https://security.debian.org/debian-security|http://mirrors.cloud.tencent.com/debian-security|g' \
            /etc/apt/sources.list.d/debian.sources; \
    fi && \
    apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY docker/manage.py .
COPY trendradar/ ./trendradar/

# 复制 entrypoint.sh 并强制转换为 LF 格式
COPY docker/entrypoint.sh /entrypoint.sh.tmp
RUN sed -i 's/\r$//' /entrypoint.sh.tmp && \
    mv /entrypoint.sh.tmp /entrypoint.sh && \
    chmod +x /entrypoint.sh && \
    chmod +x manage.py && \
    mkdir -p /app/config /app/output

ENV PYTHONUNBUFFERED=1 \
    CONFIG_PATH=/app/config/config.yaml \
    FREQUENCY_WORDS_PATH=/app/config/frequency_words.txt

ENTRYPOINT ["/entrypoint.sh"]
