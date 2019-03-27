FROM python:2.7
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
COPY hello_python.py /code/
COPY . /code/
EXPOSE 8080
CMD python hello_python.py
