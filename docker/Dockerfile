# Use ubuntu Linux as the base image
FROM ubuntu:latest

# Install necessary packages for installing Java
RUN apt update && \
    apt install -y openjdk-11-jdk && \
    apt install -y unzip

# Set environment variables for Java
ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk-amd64
ENV PATH $JAVA_HOME/bin:$PATH

# Copy hybris zip inside the Container
COPY CXCOMM190500P_0-70004140.zip /project/CXCOMM190500P_0-70004140.zip

# Copy localextensions.xml file
COPY localextensions.xml /project/localextensions.xml

# Navigate to project directory
WORKDIR /project

# Unzip hybris zip
RUN unzip CXCOMM190500P_0-70004140.zip && rm CXCOMM190500P_0-70004140.zip

# Copy b2c_developer zip inside the Container
COPY b2c_developer.zip /project/b2c_developer.zip

# Unzip b2c_developer.zip
RUN unzip b2c_developer.zip && rm b2c_developer.zip

# Move the b2c_developer directory to its correct location
RUN mv /project/b2c_developer /project/installer/recipes/b2c_developer

# Navigate to the installer directory
WORKDIR /project/installer

# Add exec permission to .sh files
RUN find ./ -type f -iname "*.sh" -exec chmod +x {} \;

# Run the install command with nimda as initial password
RUN ./install.sh -r b2c_developer -A initAdminPassword=nimda

# Navigate to the platform directory
WORKDIR /project/hybris/bin/platform/

# Add exec permission to .sh files
RUN find ./ -type f -iname "*.sh" -exec chmod +x {} \;

# Run ant clean all (also ant initialize if it's needed)
RUN . ./setantenv.sh && ant modulegen -Dinput.module=accelerator -Dinput.name=training -Dinput.package=com.example.training -Dinput.template=accelerator

# Navigate to the config directory
WORKDIR /project/hybris/config

# Replace strings in localextensions.xml file
RUN mv /project/localextensions.xml /project/hybris/config/localextensions.xml

# Expose http port
EXPOSE 9001

# Expose https port
EXPOSE 9002

# Expose Debug port
EXPOSE 8000

# Go back to platform
WORKDIR /project/hybris/bin/platform

# Run ant clean all (also ant initialize if it's needed)
RUN . ./setantenv.sh && ant clean all initialize