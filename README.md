# Catsup
Catsup is a python application that helps users to pre-process pathogen sequencing data to use [SP3](https://catdocs.readthedocs.io/en/latest/index.html), Scalable Pathogen Pipeline Platform. It prepares meta-data, removes human reads and uploads sequencing fastq files to a dedicated storage, for example, a S3 bucket.

## Catsup functions

### Prepare the template
1. Generates a template for data submission
2. Validates the template after user input

### Anonymizing sample identifier
3. Creates unique identifier (UUID) for submission
4. Creates unique identifier (UUID) for every sample
5. Generate md5sum for each sample file
6. Update the template with submission UUID, sample guid and md5sum
7. Rename/symlink sample files with sample UUID
8. Generates a lookup table of sample name and sample guid

### Removing human reads
9. Run quality control pipeline (trimming and remove human reads)

### Upload to a S3 bucket
10. Upload cleaned (no human reads) sample files to S3 buckets

## User template example

| index | subindex | sample_name | sample_filename | sample_file_extension | sample_host | sample_collection_date | sample_country | submission_title | submission_description | submitter_organisation | submitter_email | instrument_platform | instrument_model | instrument_flowcell |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
1 | 1 | P0001 | in/P0001_1.fastq.gz | fastq.gz | Homo sapiens | 2020-01-30 | United Kingdom | Bacteria infection study | Bacteria infection study for drug resistance | University of Oxford | crookit@ndm.ox.ac.uk | Illumina pair-ended sequencing | Illumina HiSeq 4000 | 96
1 | 2 | P0001 | in/P0001_2.fastq.gz | fastq.gz | Homo sapiens | 2020-01-30 | United Kingdom | Bacteria infection study | Bacteria infection study for drug resistance | University of Oxford | crookit@ndm.ox.ac.uk | Illumina pair-ended sequencing | Illumina HiSeq 4000 | 96
2 | 1 | P0002 | in/P0002_1.fastq.gz | fastq.gz | Homo sapiens | 2020-01-30 | United Kingdom | Bacteria infection study | Bacteria infection study for drug resistance | University of Oxford | crookit@ndm.ox.ac.uk | Illumina pair-ended sequencing | Illumina HiSeq 4000 | 96
2 | 2 | P0002 | in/P0002_2.fastq.gz | fastq.gz | Homo sapiens | 2020-01-30 | United Kingdom | Bacteria infection study | Bacteria infection study for drug resistance | University of Oxford | crookit@ndm.ox.ac.uk | Illumina pair-ended sequencing | Illumina HiSeq 4000 | 96
3 | 1 | P0003 | in/P0003_1.fastq.gz | fastq.gz | Homo sapiens | 2020-01-30 | United Kingdom | Bacteria infection study | Bacteria infection study for drug resistance | University of Oxford | crookit@ndm.ox.ac.uk | Illumina pair-ended sequencing | Illumina HiSeq 4000 | 96
3 | 2 | P0003 | in/P0003_2.fastq.gz | fastq.gz | Homo sapiens | 2020-01-30 | United Kingdom | Bacteria infection study | Bacteria infection study for drug resistance | University of Oxford | crookit@ndm.ox.ac.uk | Illumina pair-ended sequencing | Illumina HiSeq 4000 | 96

## UUID and sample lookup table example

Sample names and sample file names are renamed to UUIDs.

The map between sample names and UUIDs are written to a file named sample_uuid_map.csv, which is to be kept by the user to allow mapping processed samples back to sample names.

|sample_name|sample_uuid4|original_file|renamed_file|
| - | - | - | - |
|P0001 | 87e22c5e-3a02-4edb-ad9d-303a36d9f7ee | in/P0001_1.fastq.gz | 87e22c5e-3a02-4edb-ad9d-303a36d9f7ee_1.fastq.gz|
|P0001 | 87e22c5e-3a02-4edb-ad9d-303a36d9f7ee | in/P0001_2.fastq.gz | 87e22c5e-3a02-4edb-ad9d-303a36d9f7ee_2.fastq.gz|
|P0002 | 3310d206-c1e9-4b41-9efc-4c8da7eca3c4 | in/P0002_1.fastq.gz | 3310d206-c1e9-4b41-9efc-4c8da7eca3c4_1.fastq.gz|
|P0002 | 3310d206-c1e9-4b41-9efc-4c8da7eca3c4 | in/P0002_2.fastq.gz | 3310d206-c1e9-4b41-9efc-4c8da7eca3c4_2.fastq.gz|
|P0003 | fff6ead2-d1ec-470b-b0a1-8935dbbe3212 | in/P0003_1.fastq.gz | fff6ead2-d1ec-470b-b0a1-8935dbbe3212_1.fastq.gz|
|P0003 | fff6ead2-d1ec-470b-b0a1-8935dbbe3212 | in/P0003_2.fastq.gz | fff6ead2-d1ec-470b-b0a1-8935dbbe3212_2.fastq.gz|

## Anonymized meta-data file example

|submission_uuid4|sample_uuid4|index|subindex|sample_filename|sample_file_extension|sample_host|sample_collection_date|sample_country|submission_title|submission_description|submitter_organisation|submitter_email|instrument_platform|instrument_model|instrument_flowcell|original_file_md5|original_file_sha1|original_file_sha512|clean_file_md5|clean_file_sha1|clean_file_sha512|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|cac80c10-4e19-4dfc-9b34-631f85ad6760|c6f24a54-3a6d-4676-9024-2dec6a51830b|1|1|c6f24a54-3a6d-4676-9024-2dec6a51830b_C1.fastq.gz|fastq.gz|Homo sapiens|2020-01-30|United Kingdom|Bacteria infection study|Bacteria infection study for drug resistance|University of Oxford|crookit@ndm.ox.ac.uk|Illumina pair-ended sequencing|Illumina HiSeq 4000|96|ff69240cfa3c0c8e3af5ba8dd6e14f66|c685ab900f85be7c14d3858049ed4a1477b5166a|a114d852a84e079f3a4698d6ce50049e81b644b5da542a1a9291db9c1217177a3790ae212ad29ec6777dd844a49b52c42b6b397886872cac5541ca89a8c2b37b|53feca1629efd020d208a526c20da40d|1422dfd5f5ae2484b8e382615c27a81214c0d69e|966f617d8a851c50e0f0b37c97131f94fe601f0fd0b045c75147f54d14e74542e807b9fca9ef8ce7caccdaf29fb95597449c06c85d198e0cd1a0bc8c7c17d35c|
|cac80c10-4e19-4dfc-9b34-631f85ad6760|c6f24a54-3a6d-4676-9024-2dec6a51830b|1|2|c6f24a54-3a6d-4676-9024-2dec6a51830b_C2.fastq.gz|fastq.gz|Homo sapiens|2020-01-30|United Kingdom|Bacteria infection study|Bacteria infection study for drug resistance|University of Oxford|crookit@ndm.ox.ac.uk|Illumina pair-ended sequencing|Illumina HiSeq 4000|96|37fd3ecf43dd788d142580e7ad3a0a01|b681ae3a1dc3afd6e810ada49cabe7debcc3bb67|9dd99008a4e445efb1406e0ae2ad8a65e2f3bdbff69197da0fc7db4416130630e2e19e17c2f01739b1d8f4a623cac8ebef3dae6e2915fe62590f2f7b2f035cc9|c65d2b64e05c7509f01148b7841374b1|a226c753d3c02f4c4111b89e063693a0eb3e3442|56ec52e9f164cee6835f62b958bfbcb8c482f0b78fb5cc183b6a2f9399b5059dbcf9ace637461b30cf086f074dbb5646b701d398b7684a33ba2c9ae9454acadb|
|cac80c10-4e19-4dfc-9b34-631f85ad6760|b1acabd2-6476-429b-b230-a27295399cd6|2|1|b1acabd2-6476-429b-b230-a27295399cd6_C1.fastq.gz|fastq.gz|Homo sapiens|2020-01-30|United Kingdom|Bacteria infection study|Bacteria infection study for drug resistance|University of Oxford|crookit@ndm.ox.ac.uk|Illumina pair-ended sequencing|Illumina HiSeq 4000|96|794c1f481d37cb7b4ed3f9a362391db4|94f26aa29cc493ff40c063cba1a321c9da63612f|83e7a5d1200ee2d215070c4ee0a6b60b52dcc50abc2d73b5ea8a85fa255e1ccf97f4166368297f0be69daaaae4489e51400e2a7194e375077484212c92579212|9b8baa1ff1cf83b4503c8dbd08164668|2a01fa6f446237de93bc6e8d8419c322acf11385|6d2bedc509a19030a6b03e04c7a1a31cd57010361dd6dfe53cdd921e2d93689f38973dbe85e817b836d4a32d5aee7962048d2e74be63de6068a2dbadf25e5f99|
|cac80c10-4e19-4dfc-9b34-631f85ad6760|b1acabd2-6476-429b-b230-a27295399cd6|2|2|b1acabd2-6476-429b-b230-a27295399cd6_C2.fastq.gz|fastq.gz|Homo sapiens|2020-01-30|United Kingdom|Bacteria infection study|Bacteria infection study for drug resistance|University of Oxford|crookit@ndm.ox.ac.uk|Illumina pair-ended sequencing|Illumina HiSeq 4000|96|3db410310975d6edf6fad65a2c9995d6|2760b9253afece26830849d9fcc21eaf9eabcd84|182ae9e06e274974fb634428814320f76fc6c05ce08ce8ab4fd0ab0587449cebc459c4714db9afc9c105a2f76914aa7bb3801a2c0b146f18e79088a035ad5172|0bda6f69f715cc9c14e305551c7e6b22|054be3a1fcd561f3aed836e68d4a2dd077d162fa|e646ccaa63329f8fed9f5cfde8ae6b4ca98b936722024a845cc54ef42c5829e97f7065b94184e402e5ae905a3441c4c2d5d4bd682941cce3bc8b8cbe1d711aa2|
|cac80c10-4e19-4dfc-9b34-631f85ad6760|e6d24ef0-347d-4f6d-8de1-6af0268ceaf7|3|1|e6d24ef0-347d-4f6d-8de1-6af0268ceaf7_C1.fastq.gz|fastq.gz|Homo sapiens|2020-01-30|United Kingdom|Bacteria infection study|Bacteria infection study for drug resistance|University of Oxford|crookit@ndm.ox.ac.uk|Illumina pair-ended sequencing|Illumina HiSeq 4000|96|8a47b2eb219d240af161e3f9b17ea07c|5365e6340612dd59565aaec52e6c64ff7389f962|91cc53bf6366e9914a3a0deaab28f5f7236d5bc5ef7ab364c8b64bb7857d933c395ae22b758d3a7995ea87393169ea243dd580fcfa14c3943c0c465329cc16a0|0890563bc8d58ff2f68097039443f709|3d9f4ac2b41f66d0e1016def9a35f2edc185d177|75ab37dc73fdcf9cb1c899eeaee8be18f0f6f90849c618821bec4284b4974f9065b57001fbcb3dcc1acc47b24c67eaa5105197d5299239552ab5d77b5852d647|
|cac80c10-4e19-4dfc-9b34-631f85ad6760|e6d24ef0-347d-4f6d-8de1-6af0268ceaf7|3|2|e6d24ef0-347d-4f6d-8de1-6af0268ceaf7_C2.fastq.gz|fastq.gz|Homo sapiens|2020-01-30|United Kingdom|Bacteria infection study|Bacteria infection study for drug resistance|University of Oxford|crookit@ndm.ox.ac.uk|Illumina pair-ended sequencing|Illumina HiSeq 4000|96|9d919ceff5ea330d41661e7026a22354|5e76f50091a6d9842b8e28a6d0d7fe521e8985ed|ba8772724f7b2e238faa1af4420a811a9bf84adecb659f4523632a8e33039abd202b8709fbfeb8c4736106322290ab57c0419e13820cdcb0797a61eaf8db835c|9578832af2be1359d3cdb51954987b84|ac139285d09ba06f9373192d2b11590ffd17eec5|5d0803246d320947116e34f4178c7b027e412b38c9a1c01d16d6961bb3c8a52b38c7525c76291a4065e91d2c82993a1a230c7ed8d677f2b40ba96072aa0553f6|
