FROM ubuntu:20.04

ENV TF_VERSION 0.13.3
ENV TF_ZIP_SHA256 35c662be9d32d38815cde5fa4c9fa61a3b7f39952ecd50ebf92fd1b2ddd6109b

LABEL ubuntu="20.04"
LABEL terraform="$TF_VERSION"

ENV TZ=Europe/London
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update  --yes && \
	apt-get install --yes --no-install-recommends \
	ca-certificates \
	awscli \
	jq \
	curl \
	dnsutils \
	unzip \
	gpg \
	gpg-agent \
	golang \
	git

WORKDIR /tmp

RUN curl https://releases.hashicorp.com/terraform/${TF_VERSION}/terraform_${TF_VERSION}_linux_amd64.zip > terraform.zip && \
	echo "${TF_ZIP_SHA256}  terraform.zip"  > terraform.sha && \
	sha256sum -c terraform.sha && unzip terraform.zip && mv terraform /usr/bin/terraform                    && \
	rm terraform.zip && rm terraform.sha

RUN GO111MODULE=on go get -v github.com/camptocamp/terraform-provider-pass && \
	mkdir -p ~/.terraform.d/plugins/linux_amd64 && \
	mv ~/go/bin/terraform-provider-pass ~/.terraform.d/plugins/linux_amd64/

# prom-ec2 terraform expects a pub ssh key even if it doesn't use it
RUN mkdir -p $HOME/.ssh/ && touch $HOME/.ssh/id_rsa.pub

COPY assume-role /usr/bin/assume-role

ENTRYPOINT ["bash"]
