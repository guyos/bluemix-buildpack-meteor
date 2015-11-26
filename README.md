# Bluemix buildpack for Meteor

## Supported version

This buildpack is dedicated for use with Meteor 1.0+.

## Usage

* Create Meteor application

```
$ meteor create testmeteor
testmeteor: created.

To run your new app:
   cd testmeteor
   meteor
```
* Run and test the application in your local until all functions are stistisfied

* Create Mongodb service in Bluemix dashboad. The service id will be use in the next step. 

* Create manifest.yml and put it under the testmeteor folder.
Here is the sample yml.
```
---
applications:
- memory: 1GB
  domain: mybluemix.net
  path: .
  buildpack: https://github.com/alexbonti/bluemix-buildpack-meteor
  host: testmeteor
  name: testmeteor
  disk: 512M
  services:
    - MongoLab-4d
  instances: 1
```
* Create .cfignore to exclude the path 'local' to be uploaded
```
.meteor/local
```
* In testmeteor/.meteor folder, open the platforms file then remove the ios and android entries (if there exist)

* Under testmeter, Run 
```
cf push

```

#!/usr/bin/env bash

echo "MongoDB Service Binding" | indent
mkdir -p "$BUILD_DIR"/.profile.d
cat | tee "$BUILD_DIR"/.profile.d/mongo.sh <<EOF
  #!/bin/sh
  export ROOT_URL=\${ROOT_URL:-`ruby -e 'require "json"; puts "http://#{JSON.parse(ENV["VCAP_APPLICATION"])["application_uris"][0]}"'`}
  export MONGO_URL=\${MONGO_URL:-`ruby -e "require 'json'; puts JSON.parse(ENV['VCAP_SERVICES']).find { |key,value| value[0]['label'].eql? 'mongodb-2.4' }[1][0]['credentials']['url']"`}
EOF
