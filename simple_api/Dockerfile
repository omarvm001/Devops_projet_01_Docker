# Use the Python 3.8 base image with Debian Buster
FROM python:3.8-buster

# Add a label to specify the author of the image
LABEL org.opencontainers.image.authors="Omar Bouchenak omar.vm.001@gmail.com"

# Copy the source code of the API in the container at the root 
COPY ./student_age.py /

# Copy requirements.txt file into the root directory of the container
COPY requirements.txt /

# Update packages and install necessary dependencies
RUN apt update -y && apt install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y

# Install required packages
RUN pip3 install -r /requirements.txt

# Creation of the persistent data folder
VOLUME [ "/data" ]

# Expose port 5000 to allow incoming connections
EXPOSE 5000

# Define the default command to execute when the container starts
#ENTRYPOINT [ "python" ]

# Default arguments to pass to the command when the container starts
CMD [ "python3", "student_age.py" ]
