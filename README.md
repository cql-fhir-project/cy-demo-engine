## Cy-demo-engine
Demonstrates the ability to translate and execute.

##### Disclaimer
This is not a production ready solution!
This Docker container for demonstrating and testing purpose only!

##### Quick start
1. Install Docker and docker-compose on your PC:
    * on your Windows:
    1. Google how to install Docker and docker-compose on Windows
    (it's pretty simple)
    * on Ubuntu:
    1. $ `curl -fsSL https://get.docker.com | bash`
    1. $ `systemctl enable docker.service`
    1. $ `systemctl start docker.service`
    1. $ ```sudo curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose```
    1. $ `sudo chmod +x /usr/bin/docker-compose`
    1. $ `sudo curl -L https://raw.githubusercontent.com/docker/compose/1.27.4/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose`
1. clone this repository `git clone https://github.com/cql-fhir-project/cy-demo-engine`
1. `cd cy-demo-engine`
1. `docker-compose up`

Now you should see output that demonstrates 2 stages:
* `translation` of all the cql files in `./measure-packages/DiabeticFootExam-1.0.0` (by default)  
* `execution` against diabetic mock bundles (by default)  
    
#### How to change default behaviour
###### Adding new measure package:
1. Create a new sub-folder in `measure-packages`. For example, `BCS_Logic-0.9.000`
1. Copy `BCS_Logic-0.9.000.cql` file as `main.cql` into `BCS_Logic-0.9.000` folder.
1. Copy all included libraries (FHIR helpers) into created above folder `BCS_Logic-0.9.000` 
(since now I call the `BCS_Logic-0.9.000` as measure package).
Be careful, follow the convention naming fhir-helper libraries. See `main.cql` file for a clue. 
For instance, `include FHIRHelpers version '4.0.1' called FHIRHelpers` - means that there HAS to be 
the `FHIRHelpers-4.0.1.cql` file included as a measure package dependency.
1. Include Value Set files into `vsac_chache`. 
1. To activate new measure package 
change `MEASURE_PACKAGE` variable from `DiabeticFootExam-1.0.0` to `BCS_Logic-0.9.000` in `docker-compose.yml` file.

###### Increasing population:
1. If you need include some more population, then use [Synthea](https://github.com/synthetichealth/synthea)
to generate some or  [download it](https://storage.googleapis.com/synthea-public/synthea_sample_data_fhir_r4_sep2019.zip) 
and save into `bundles` folder. 
1. To activate synthetic population uncomment `- ./bundles/:/usr/src/app/bundles/` line in `docker-compose.yml` file.
1. Finally, run `docker-compose up` again.

#### Notes
1. You don't have to run all the stages (`translation` and `execution`). 
Providing prepared EML files as a measure package allows you to skip the `translation` stage. 
1. You don't have to include UMLS `valuesets` unless you provide `UMLS_API_KEY` (see docker-compose.yml file)
1. The `cql-execution` library doesn't currently have any support for VSD files. 
However, the cql-execution library includes a very simple CodeService 
that accepts a JSON-formatted map of value sets (by OID and then version).

One approach to supporting VSD files would be to write a script that can parse them into the JSON format 
that the CodeService expects.

Another approach would be to write our own CodeService implementation (like cql-exec-vsac) that knows how to read VSD files. 
This approach might be more efficient (depending on implementation) but is likely more work.


