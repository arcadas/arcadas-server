FROM php:7.2-fpm

RUN apt-get update
RUN apt-get -y install wget gnupg2 ffmpeg rsync perl
RUN wget -q -O - https://mkvtoolnix.download/gpg-pub-moritzbunkus.txt | apt-key add -
RUN apt-get update
RUN apt -y install mkvtoolnix
