
[[GKE wemo course note]]
[[Google VPC 架構]]



# 常見指令
```sh


# 看現在是在那個 gcp a/c
gcloud auth list

# 如果不在你要的 a/c, 切 a/c
gcloud config set account ben.hu@wemoscooter.com
gcloud config set account re4388.103.0406@gmail.com

# login to adc
gcloud auth application-default login

# 要用翻譯，需要指定 quota project
gcloud auth application-default set-quota-project  inner-topic-419501


# 整套
gcloud config set account re4388.103.0406@gmail.com 
local translated=$(bun /Users/re4388/project/personal/lang/bun/bun_cli_0/poc/translate/translateC2E.ts "如果您需要更精细地控制输出")
local translated=$(bun /Users/re4388/project/personal/lang/bun/bun_cli_0/poc/translate/eng_to_var.ts "$translated")
gcloud config set account ben.hu@wemoscooter.com

```



# 保留 ip 設定

![[IMG-gcp_index-20250811204941578.png]]


# 建立 instance log 2024_04_27
- 建立 ip forward
- 使用 ubuntu
- 用 e2-micro即可
- 台灣，除非有特殊需求

```sh

gcloud compute instances create instance-2024_06_08-17_57_25 \
    --project=inner-topic-419501 \
    --zone=asia-east1-b \
    --machine-type=e2-medium \
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
    --can-ip-forward \
    --maintenance-policy=MIGRATE \
    --provisioning-model=STANDARD \
    --service-account=660421517660-compute@developer.gserviceaccount.com \
    --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
    --tags=http-server,https-server \
    --create-disk=auto-delete=yes,boot=yes,device-name=instance-20240526-054054,image=projects/ubuntu-os-cloud/global/images/ubuntu-2004-focal-v20240519,mode=rw,size=10,type=projects/inner-topic-419501/zones/asia-east1-b/diskTypes/pd-balanced \
    --no-shielded-secure-boot \
    --shielded-vtpm \
    --shielded-integrity-monitoring \
    --labels=goog-ec-src=vm_add-gcloud \
    --reservation-affinity=any



# delete ip
gcloud compute addresses delete my-ip-0  --project main-cyclist-388414 --region asia-east1


```

![[IMG-gcp_index-20250811204941703.png| 300]]
22,7000,41322,7070,2017,54321,2489


# new google a/c for gcp 2024_04_06
``` sh

google a/c
re4388benhu


mail
re4388.103.0406@gmail.com


2aoxjgju_103_0406

pass: 2aoxjgju_103_0406


```



# vm 安裝 docker
 ```
curl -sSL https://get.docker.com/ | sh
```
[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
[https://stackoverflow.com/a/43639310/7621417](https://stackoverflow.com/a/43639310/7621417)

# yt channel and resource
- [Serverless Expeditions - YouTube](https://www.youtube.com/playlist?list=PLIivdWyY5sqJwq_pgOxcHzusWjXDVCEiX)
- [Build With Google Cloud - YouTube](https://www.youtube.com/playlist?list=PLIivdWyY5sqLuHY9Lw3DSJbY9Y-eIGMKf)
- [How to build digital e-commerce platform on Google Cloud - YouTube](https://www.youtube.com/watch?v=aOaR4GAcKYU)
- [How to create a microservice architecture with Google Cloud - YouTube](https://www.youtube.com/watch?v=YxNRkw8Msdw)
- [Twitter's GCP Architecture for Its Petabyte-Scale Data Storage in GCS (Cloud Next '19) - YouTube](https://www.youtube.com/watch?v=rBNFwdVDlyo)
- [cloud-architect-gcp/Best-Practices.md at master · jorwalk/cloud-architect-gcp](https://github.com/jorwalk/cloud-architect-gcp/blob/master/Best-Practices.md)



# exam link
- Home Page: [https://cloud.google.com/certification/cloud-engineer](https://cloud.google.com/certification/cloud-engineer) 
- Exam Guide: [https://cloud.google.com/certification/guides/cloud-engineer](https://cloud.google.com/certification/guides/cloud-engineer) 
- Sample Questions: [https://cloud.google.com/certification/sample-questions/cloud-engineer](https://cloud.google.com/certification/sample-questions/cloud-engineer) 
- Registering For Exam: [https://support.google.com/cloud-certification/#topic=9433215](https://support.google.com/cloud-certification/#topic=9433215)
- [GCP Associate Cloud Engineer (ACE) 认证考试心得 - 知乎](https://zhuanlan.zhihu.com/p/478981425)
- [GCP Associate Cloud Engineer Prepara Note - LiuYuchen HP](https://liuyuchen777.github.io/2022/02/20/ACE-Prepare-Note/#Other)
- [Google Cloud Associate Engineer Exam Notes (GCP ACE) | by GowthamLabs | GowthamLabs | Medium](https://medium.com/gowthamlabs/google-cloud-associate-engineer-notes-gcp-ace-8d2260cb3882)

Questions
- 186 Questions: [Google Associate Cloud Engineer Free Certification Exam Material | ExamTopics](https://link.zhihu.com/?target=https%3A//www.examtopics.com/exams/google/associate-cloud-engineer/)
- Official Sample Questions: [Associate Cloud Engineer Sample Questions (google.com)](https://link.zhihu.com/?target=https%3A//docs.google.com/forms/d/e/1FAIpQLSfexWKtXT2OSFJ-obA4iT3GmzgiOCGvjrT9OfxilWC1yPtmfQ/viewform%3Ffbzx%3D-6318940972985920607)
- GCP EXAM QUESTIONS[Google Associate Cloud Engineer Practice Exam Part 1 (gcp-examquestions.com)](https://link.zhihu.com/?target=https%3A//gcp-examquestions.com/gcp-associate-cloud-engineer-practice-exam-part-1/)[Google Associate Cloud Engineer Practice Exam Part 2 (gcp-examquestions.com)](https://link.zhihu.com/?target=https%3A//gcp-examquestions.com/gcp-associate-cloud-engineer-practice-exam-part-2/)[Google Associate Cloud Engineer Practice Exam Part 3 (gcp-examquestions.com)](https://link.zhihu.com/?target=https%3A//gcp-examquestions.com/gcp-associate-cloud-engineer-practice-exam-part-3/)[Google Associate Cloud Engineer Practice Exam Part 4 (gcp-examquestions.com)](https://link.zhihu.com/?target=https%3A//gcp-examquestions.com/gcp-associate-cloud-engineer-practice-exam-part-4/)[Google Associate Cloud Engineer Practice Exam Part 5 (gcp-examquestions.com)](https://link.zhihu.com/?target=https%3A//gcp-examquestions.com/gcp-associate-cloud-engineer-practice-exam-part-5/)[Google Associate Cloud Engineer Practice Exam Part 6 (gcp-examquestions.com)](https://link.zhihu.com/?target=https%3A//gcp-examquestions.com/gcp-associate-cloud-engineer-practice-exam-part-6/)


exam practice link
- [Preparing for Your Associate Cloud Engineer Journey | Google Cloud Skills Boost](https://www.cloudskillsboost.google/course_templates/77?utm_source=gcp_training&utm_medium=website&utm_campaign=cgc&skip_cache=true)
    - [https://www.cloudskillsboost.google/course_sessions/2025202/documents/341552](https://www.cloudskillsboost.google/course_sessions/2025202/documents/341552)
    [Module 0: Introduction](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_0_v2.0.pdf) [Module 1: Se ing Up a Cloud Solution Environment](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_1_v2.0.pdf) [Module 2: Planning and Con guring a Cloud Solution](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_2_v2.0.pdf) [Module 3: Deploying and Implementing a Cloud Solution](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_3_v2.0.pdf) [Module 4: Ensuring Successful Operation of a Cloud Solution](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_4_v2.0.pdf) [Module 5: Con guring Access and Security](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_5_v2.0.pdf) [Module 6: Your next steps](https://storage.googleapis.com/cloud-training/gcpace/2.0/en/on-demand/Preparing_for_ACE_Module_6_v2.0.pdf)
- [Free Google Associate Cloud Engineer GCP Examl | ExamTopics](https://www.examtopics.com/exams/google/associate-cloud-engineer/)
- [Associate Cloud Engineer Sample Questions](https://docs.google.com/forms/d/e/1FAIpQLSfexWKtXT2OSFJ-obA4iT3GmzgiOCGvjrT9OfxilWC1yPtmfQ/viewform)
    [Associate Cloud Engineer Sample Questions.pdf](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d698aa7f-e7bf-460d-ae10-4dfc17b36693/Associate_Cloud_Engineer_Sample_Questions.pdf)
- [Cloud Engineer Learning Path | Google Cloud Skills Boost](https://www.cloudskillsboost.google/paths/11)