FROM adoptopenjdk/openjdk11:debian
EXPOSE 8080 
RUN mkdir /app
RUN useradd --system --user-group javauser
COPY target/*.jar /app/app.jar
COPY /entrypoint.sh /app/entrypoint.sh
RUN chown -R javauser:javauser /app
USER javauser
ENTRYPOINT ["/bin/sh","-c","/app/entrypoint.sh" ]
