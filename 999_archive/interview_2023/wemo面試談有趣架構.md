
[[interview_2023_idx]]




![[IMG-wemo面試談有趣架構-20250810201319980.png]]

- resource
    # Resources
    The numbers of the bullet point match the numbers in the diagram above.
    ### **1. ImageFileCollection Lambda**
    1. The webapp will send request to this lambda to get ImageFileCollection data.
    2. This lambda will return the signedURL of [view-lambda-temp-data-storage S3 Bucket](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-view-lambda-temp-data-storageS3Bucket)  to the webapp, so that the webapp can get the collection data later from the S3 bucket
    ### **2. view-lambda-temp-data-storage S3 Bucket**
    1. The bucket where we temprarily store the imageFilecollection data (without imageFile)
    ### **3. ImageFileCollectionGetter Lambda**
    1. The lambda that will be invoked by [ImageFileCollection Lambda](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-ImageFileCollectionLambda)
    2. Get imageFileCollection data (without imageFile) from the source systems
    3. Store the imageFileCollection data to [view-lambda-temp-data-storage S3 bucket](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-view-lambda-temp-data-storageS3Bucket) (where the webapp will keep polling the data)
    4. If ImageFileCacheAction is "store", trigger the lazying caching by sending store messages to SeriesCache SQS
    5. If ImageFileCacheAction is "purge", send purge messages to [imageFileRemoteCache SQS](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-ImageFileRemoteCacheSQS)
    ### **4. SeriesAndImageFileInfo DynamoDB Table**
    1. The table that we store the preprocess information of series and imageFiles
    ### **5. SeriesCache SQS**
    1. Collect lazy caching messages
    ### **6. SeriesCache Dead Letter Queue**
    1. Collect messages which were failed to be processed by [SeriesCache Lambda](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-SeriesCacheLambda)
    ### **7. SeriesCache Lambda**
    1. Send request to source system to get all the imageFiles of the given series
    2. Create or requeue the imageFileInfo in DynamoDB table
    3. Send imageFile remote cache messages to [imageFileRemoteCache SQS](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-ImageFileRemoteCacheSQS)
    ### **8. ImageFileRemoteCache SQS**
    1. The queue that collects imageFile remote cache messages
    ### **9. ImageFileRemoteCache Dead Letter Queue**
    1. The queue that collects messages which were failed to be processed by [ImageFileRemoteCache Lambda](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-ImageFileRemoteCacheLambda)
    ### **10. ImageFileRemoteCache Lambda**
    1. The lambda that will send request to get the imageFile of the given content location, then store it into [view-image-file-remote-cache S3 bucket](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-view-image-file-remote-cacheS3bucket)
    ### **9. vf-remote-cache-eager-submissions S3 bucket**
    1. The bucket that will trigger the eager caching workflow when storing the complete ImageFileCollection data (with imageFile) into the bucket
    ### **10. EagerCacheCollection Lambda**
    1. The lambda that will get imageFileCollection data from [vf-remote-cache-eager-submissions S3 bucket](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-vf-remote-cache-eager-submissionsS3bucket), then send eager caching messages to [ImageFileRemoteCache SQS](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-ImageFileRemoteCacheSQS)
    ### **11. view-image-file-remote-cache S3 bucket**
    1. The main bucket that we store all the caching data and files (ex: DICOM file, thumbnail.png, wadors metadata....)
    2. When the imageFile is successfully uploaded, the bucket will automatically trigger the [PreprocessImageFile Lambda](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-PreprocessImageFileLambda) (s3:ObjectCreated Event)
    ### **12. PreprocessImageFile Lambda (python)**
    1. The lambda that generate the thumbnail and wadors metadata of the given imageFile
    ### **13. ReconcileSeriesInfo Lambda**
    1. The lambda that will check the preprocessStatus of all the imageFileInfo of the given series
    2. If all the imageFiles are successfully preprocessed, update the series preprocessStatus to completed
    3. Get the seriesNumber and numberOfSlices of the series and set them into SeriesInfo
    ### **14. SeriesImageFileRecords Lambda**
    1. The webapp will send request to this lambda to get imageFiles of a series.
    2. This lambda will return the signedURL of [vf-series-data-transfer S3 Bucket](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-vf-series-data-transferS3Bucket) to the webapp, so that the webapp can get the imageFile data later from the S3 bucket
    ### **15. vf-series-data-transfer S3 Bucket**
    1. The bucket where we temprarily store the seriesImageFileRecords data
    ### **16. SeriesImageFileRecordsGetter Lambda**
    1. The lambda that will be invoked by [SeriesImageFileRecords Lambda](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-SeriesImageFileRecordsLambda)
    2. Get imageFileRecords from source system
    3. Check the preprocessStatus of the series in [SeriesAndImageFileInfo DynamoDB Table](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-SeriesAndImageFileInfoDynamoDBTable), if series is preprocessed, replace contentLocation with signedURL5Save seriesImageFileRecords to [vf-series-data-transfer S3 Bucket](https://confluence.imgdev.bioclinica.com/display/VF/.Backend+Resources+vRAI1.2#id-.BackendResourcesvRAI1.2-vf-series-data-transferS3Bucket)


APP is a medical img viwer

it can display mehical img

mgm a patienet data by collection, timepoint, each timpoint we have many series

like sereisA is MRI imgs

seriesB is CT sacn imgs

can you can make annotation on img

modify and update meta data of a img (DICOM tag)

(we got a DICOM tag brower to see and an editor to edit)

===

in term of img display: we use 2 stage loading

it will send req to backed to tell bd what collection(patient's) he want to get

we impl a 2 stage loading stgy to speed up the perf

first stage get all collection “metadata” for a patent

second stage get all series img pixsel data

why? it take time to load all seires imgs for a colleciton many hundard and thouosand of img

=> this way, it become lazy-load/on-demand display

============

how to do it

for fd after send collection req, it will pooling from s3 and then it will get metaData for this colleciton it will assemeble all data, and maintin in its fd state

(we don't use redux, we use a fd mgmt framwork called overmind, you can think it is a simplered ver of redux, same pattern, a centrailize store and has it own getter adn setter)

- use a PQ to handle the backend fetching priporty
    - there's many rules tell which series need to load first via that PQ
        - like the default img to display
        - like user doblue click the thumbanil, it can promppt loading it
        - like the most left one first in one timeseires
        - many rules...etc and some of the series won't be activei unless user click

and get the img pixesl

save into fd state mgmt system

and assmeble them by componenet

and display on fd (using conerstone..)

(thumbnail, imgDisplay, progresBarCom…)

===

what bd need to do?

1. AWS api gateway to expose rest api
2. followed by another lambda to get patientr/collection data from source system
3. and save metadata into dynamoDB
4. dynamoDB will trigger lmabda to to preprocess those medica DICOM file, gen thmiubnail, extra metadata, extra pixsel data, all store them in s3
5. we have a deciited c++ and pytho container handle this this DICOM extraction and thumbnail gen
6. after all prepocess done, dyamoDB and S3 are updated, data store in S3, state_info and metaData in DynamoDB
7. FB can get series data from S3 ( at stage 2 API)
8. each collection and have handred of series, so our backend API use SQS and lamnda to scale to handle that kind of case
9. we will also have a centrial minotitng system
    1. each lamnda send to sqs -> lamnda send mail SES to team/SNS to user
10. since the fd is send collection level data, collection can have many series, each seresi have imagefile, 3 level…so
    1. some reconciole lambda in bewteen listen to DB/S3 update (we have 3 level actually, collection->series-> imgs) and reconcile the state of data in DB
    2. ex:
    3. like we will get img file for each series from another service
    4. after it’s done it will update dynamoDB
    5. and we can have another lambda listen to dynamoDB/or DynamoDB can have a trigger
    6. which can update the service level metaData when all imgfile in that series is done






