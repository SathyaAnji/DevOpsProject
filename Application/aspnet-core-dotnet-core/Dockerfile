FROM microsoft/aspnetcore-build:1.1 AS pre-build
WORKDIR /app
MAINTAINER Sathya sathyamamilla@gmail.com

ARG JMETER_VERSION="5.1.1"
ENV JMETER_HOME /opt/apache-jmeter-${JMETER_VERSION}
ENV	JMETER_BIN	${JMETER_HOME}/bin
ENV	JMETER_DOWNLOAD_URL  https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-${JMETER_VERSION}.tgz

# Install extra packages
# See https://github.com/gliderlabs/docker-alpine/issues/136#issuecomment-272703023
# Change TimeZone TODO: TZ still is not set!
ARG TZ="Europe/Amsterdam"
RUN    apk update \
	&& apk upgrade \
	&& apk add ca-certificates \
	&& update-ca-certificates \
	&& apk add --update openjdk8-jre tzdata curl unzip bash \
	&& apk add --no-cache nss \
	&& rm -rf /var/cache/apk/* \
	&& mkdir -p /tmp/dependencies  \
	&& curl -L --silent ${JMETER_DOWNLOAD_URL} >  /tmp/dependencies/apache-jmeter-${JMETER_VERSION}.tgz  \
	&& mkdir -p /opt  \
	&& tar -xzf /tmp/dependencies/apache-jmeter-${JMETER_VERSION}.tgz -C /opt  \
	&& rm -rf /tmp/dependencies

# TODO: plugins (later)
# && unzip -oq "/tmp/dependencies/JMeterPlugins-*.zip" -d $JMETER_HOME

# Set global PATH such that "jmeter" command is found
ENV PATH="$PATH:$JMETER_BIN"
RUN chmod 777 /app
RUN chmod 777 /opt/apache-jmeter-5.1.1/bin
RUN chmod +x /opt/apache-jmeter-5.1.1/bin

FROM pre-build AS build
WORKDIR /src
COPY ["Jmeter/EntityRepository.jmx", "Jmeter/"]
#RUN jmeter --version
#CMD cd JMETER_HOME/bin
#ADD scripts.sh /
#CMD [/scripts]
#CMD apt-get install docker.io -y 
#RUN docker run --name jmeter -v /home/vsts/work/1/s/:/jmeter egaillardon/jmeter-plugins --nongui --testfile EntityRepository.jmx --logfile result.csv -e -o test
#RUN jmeter -n -t "Jmeter/EntityRepository.jmx" -l result.csv -e -o /app
#RUN jmeter -n -t "Jmeter/EntityRepository.jmx" -l "trx;LogFileName=testresults.trx" -e -o Release

COPY . .
COPY ["aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj", "aspnet-core-dotnet-core"/]
RUN dotnet restore
FROM build AS publish
RUN jmeter -n -t "Jmeter/EntityRepository.jmx" -l results.csv -e -o /test

FROM pre-build AS final
WORKDIR /app
COPY --from=publish /test .
COPY --from=publish /test /jmetertestresults
ENTRYPOINT ["dotnet", "aspnet-core-dotnet-core.dll"]
# Copy csproj and restore as distinct layers


# Copy everything else and build
#RUN dotnet build "aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj"
#RUN dotnet publish -c Release -o out

# Build runtime image
#FROM microsoft/aspnetcore:1.1
#WORKDIR /app
#COPY --from=sathya /app/out .
