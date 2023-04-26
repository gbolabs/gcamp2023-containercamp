FROM node:latest

RUN npm install -g json-server

WORKDIR /data
VOLUME /data
COPY sample-db.json /data/db.json

EXPOSE 80

ENTRYPOINT json-server --watch /data/db.json --port 80 --host 0.0.0.0
CMD []
# /Users/gautier/sources/garaio/gcamp2023-containercamp/src/db/data
# docker run -it -p 3000:3000 -v ~/temp/gcamp-demo/:/opt/data/ gcamp2023/demo5/db