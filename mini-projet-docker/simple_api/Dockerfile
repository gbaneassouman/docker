FROM python:2.7-buster
LABEL MAINTAINER "GBANE Assouman (gbane.assouman@gmail.com)"
ADD ./student_age.py .
RUN apt update -y && apt install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y
RUN pip install  flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
VOLUME ["/data"]
EXPOSE 5000
CMD ["python","./student_age.py"]



