FROM nginx:latest
WORKDIR /app
RUN chgrp -R root /var/cache/nginx /var/run /var/log/nginx && \
    chmod -R 770 /var/cache/nginx /var/run /var/log/nginx &&\
    usermod -u 1001 nginx
COPY app.conf /etc/nginx/conf.d/
EXPOSE 8000