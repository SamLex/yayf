# Draft Examples
Some examples of how a YAYFfile from each phase _might_ look. Obviously none of this is final, and is subject to change.
This is primarily to give a general idea of functionality.

## Original Dockerfile
The Dockerfile these examples are based on can be found at https://github.com/SamLex/docker-jenkins-with-arma3-tools/blob/7c7083fbda980986304c6d0e80dbd30e232d550c/Dockerfile

## Phase 0
```yaml
baseImage:
  name: buildpack-deps
  tag: deps:stretch
stageName: PBO
env:
  - name: DEPBO_VERSION
    value: 0.6.88
execute:
  - curl -L https://armaservices.maverick-applications.com/Products/MikerosDosTools/DownloadFree.aspx?download=depbo-tools-${DEPBO_VERSION}-linux-64bit.tgz -o /root/depbo.tgz
  - tar xf /root/depbo.tgz -C /root
  - mv /root/depbo-tools-${DEPBO_VERSION} /root/depbo-tools
  - chmod +x /root/depbo-tools/bin/*
---
baseImage: "jenkins/jenkins:2.144"
labels:
  - name: maintainer
    value: "samlex <samlex@gmx.com>"
env:
  - SQFLINT_VERSION=0.3.2
user: root
execute:
  - apt-get update
  - apt-get -y --no-install-recommends install python3-pip liblzo2-2 libvorbis0a libvorbisfile3 libvorbisenc2 libogg0
  - pip3 install --no-cache-dir sqflint==${SQFLINT_VERSION}
  - apt-get -y purge python3-pip
  - apt-get clean
  - rm -rf /var/lib/apt/lists/*
files:
  - from:
      buildStage: PBO
      path: /root/depbo-tools/bin/*
    to: /usr/bin/
  - from:
      buildStage: PBO
      path: /root/depbo-tools/lib/*
    to: /usr/lib/
  - from: bin/*.bash
    to: /usr/bin/
---
user: jenkins
execute:
  - /usr/local/bin/install-plugins.sh warnings
files:
  - from: config/*.xml
    to: /usr/share/jenkins/ref/
```

## Phase 1
### Variables.yml
```yaml
DePBOVersion: 0.6.88
SQFLintVersion: 0.3.2
```

### YAYFfile
```yaml
baseImage:
  name: buildpack-deps
  tag: deps:stretch
stageName: PBO
execute:
  - curl -L https://armaservices.maverick-applications.com/Products/MikerosDosTools/DownloadFree.aspx?download=depbo-tools-{{.DePBOVersion}}-linux-64bit.tgz -o /root/depbo.tgz
  - tar xf /root/depbo.tgz -C /root
  - mv /root/depbo-tools-{{.DePBOVersion}} /root/depbo-tools
  - chmod +x /root/depbo-tools/bin/*
---
baseImage: "jenkins/jenkins:2.144"
labels:
  - name: maintainer
    value: "samlex <samlex@gmx.com>"
user: root
execute:
  - apt-get update
  - apt-get -y --no-install-recommends install python3-pip liblzo2-2 libvorbis0a libvorbisfile3 libvorbisenc2 libogg0
  - pip3 install --no-cache-dir sqflint=={{.SQFLintVersion}}
  - apt-get -y purge python3-pip
  - apt-get clean
  - rm -rf /var/lib/apt/lists/*
files:
  - from:
      buildStage: PBO
      path: /root/depbo-tools/bin/*
    to: /usr/bin/
  - from:
      buildStage: PBO
      path: /root/depbo-tools/lib/*
    to: /usr/lib/
  - from: bin/*.bash
    to: /usr/bin/
---
user: jenkins
execute:
  - /usr/local/bin/install-plugins.sh warnings
files:
  - from: config/*.xml
    to: /usr/share/jenkins/ref/
```

## Phase 2
### Variables.yml
```yaml
DePBOVersion: 0.6.88
SQFLintVersion: 0.3.2
```

### YAYFfile
```yaml
baseImage: "jenkins/jenkins:2.144"
labels:
  - name: maintainer
    value: "samlex <samlex@gmx.com>"
user: root
execute:
  - apt-get update
  - apt-get -y --no-install-recommends install python3-pip liblzo2-2 libvorbis0a libvorbisfile3 libvorbisenc2 libogg0
  - pip3 install --no-cache-dir sqflint=={{.SQFLintVersion}}
  - apt-get -y purge python3-pip
  - apt-get clean
  - rm -rf /var/lib/apt/lists/*
files:
  - from:
      remote: https://armaservices.maverick-applications.com/Products/MikerosDosTools/DownloadFree.aspx?download=depbo-tools-{{.DePBOVersion}}-linux-64bit.tgz
      unpack: true
      SHA256Hash: 01ba4719c80b6fe911b091a7c05124b64eeece964e09c058ef8f9805daca546b
      path: depbo-tools-{{.DePBOVersion}}/bin/*
    to: /usr/bin/
  - from:
      remote: https://armaservices.maverick-applications.com/Products/MikerosDosTools/DownloadFree.aspx?download=depbo-tools-{{.DePBOVersion}}-linux-64bit.tgz
      unpack: true
      SHA256Hash: 01ba4719c80b6fe911b091a7c05124b64eeece964e09c058ef8f9805daca546b
      path: depbo-tools-{{.DePBOVersion}}/lib/*
    to: /usr/lib/
  - from: bin/*.bash
    to: /usr/bin/
---
user: jenkins
execute:
  - /usr/local/bin/install-plugins.sh warnings
files:
  - from: config/*.xml
    to: /usr/share/jenkins/ref/
```

## Phase 3
### Variables.yml
```yaml
DePBOVersion: 0.6.88
SQFLintVersion: 0.3.2
```

### YAYFfile
```yaml
baseImage: "jenkins/jenkins:2.144"
labels:
  - name: maintainer
    value: "samlex <samlex@gmx.com>"
imageDialect: apt
user: root
packages:
  - name: python3-pip
    temporary: true
  - liblzo2-2
  - libvorbis0a
  - libvorbisfile3
  - libvorbisenc2
  - libogg0
execute:
  - pip3 install --no-cache-dir sqflint=={{.SQFLintVersion}}
files:
  - from:
      remote: https://armaservices.maverick-applications.com/Products/MikerosDosTools/DownloadFree.aspx?download=depbo-tools-{{.DePBOVersion}}-linux-64bit.tgz
      unpack: true
      SHA256Hash: 01ba4719c80b6fe911b091a7c05124b64eeece964e09c058ef8f9805daca546b
      path: depbo-tools-{{.DePBOVersion}}/bin/*
    to: /usr/bin/
  - from:
      remote: https://armaservices.maverick-applications.com/Products/MikerosDosTools/DownloadFree.aspx?download=depbo-tools-{{.DePBOVersion}}-linux-64bit.tgz
      unpack: true
      SHA256Hash: 01ba4719c80b6fe911b091a7c05124b64eeece964e09c058ef8f9805daca546b
      path: depbo-tools-{{.DePBOVersion}}/lib/*
    to: /usr/lib/
  - from: bin/*.bash
    to: /usr/bin/
---
user: jenkins
execute:
  - /usr/local/bin/install-plugins.sh warnings
files:
  - from: config/*.xml
    to: /usr/share/jenkins/ref/
```
