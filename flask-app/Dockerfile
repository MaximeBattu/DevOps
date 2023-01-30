# Téléchargement de l'image linux
FROM alpine:3.6

# Installation de pip avec python2
RUN apk add --update py2-pip

# Installation des requirements
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt

# Installation de l'application
COPY app.py /usr/src/app
COPY templates/index.html /usr/src/app/templates/

# Exposition du port 5000
EXPOSE 5000

# Lancement de l'application
CMD ["python", "/usr/src/app/app.py"]
