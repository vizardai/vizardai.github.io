---
layout: api
title: Open API | Vizard.ai
description: An AI-powered video generator and editor that turns one long video into 10+ clips instantly.
keywords: open api, video editing, video editor, text-based editor, online screen record, screen recorder, webinar recording, interview recording, testimonial recording, conference recording, repurpose video, video repurposing, Typestudio alternative, Descript alternative, video subtitles, resize video, edit video by text
---

# Quickstart {#quickstart}

## Step 1: Generate your API key {#generate-api-key}

1\. Navigate to your **Workspace-Invite-API**

2\. Click the "**Get API Key**" button. Your unique API key will be displayed within seconds. 

&nbsp;&nbsp;&nbsp;&nbsp;**Note**: This feature requires a Pro plan.

**Important**: Treat your API key like a password. Keep it confidential and do not share it with anyone.

## Step 2: Post a long video {#post-a-long-video}

### Request {#post-a-long-video-request}

**URL**

```python
https://elb-api.vizard.ai/hvizard-server-front/open-api/v1/project/create
```

**Method:** POST

**Rate Limits:** 3 requests per minute

**Hedaers**

| Name of Header           | Value                  | Required |
|---------------------------|------------------------|----------|
| Content-Type              | application/json       | YES      |
| VIZARDAI_API_KEY          | Vizard ai api key     | YES      |

**Body (Raw json)**

| Name           | DataType | Required | Description                                                                                                         |
|----------------|----------|----------|---------------------------------------------------------------------------------------------------------------------|
| lang           | string   | YES      | Language code of video.                                                                                             |
| videoUrl       | string   | YES      | The URL of the remote video should start with either http or https and must be downloadable.                        |
| ext            | string   | YES      | The extension of the video file. Options: mp4, 3gp, avi, mov.                                                       |
| preferLength   | array    | YES      | The duration of the clipped video: <br/> 0, auto; <br/> 1, less than 30s; <br/> 2, 30s to 60s; <br/> 3, 60s to 90s; <br/> 4, 90s to 3min. |
| projectName    | string   | NO       | The name of the long video.                                                                                         |
| subtitleSwitch | int      | NO       | Subtitle switch. <br/> 0, off; <br/> 1, on; (default value)                                                                     |
| headlineSwitch | int      | NO       | Headline switch. <br/> 0, off; <br/> 1, on; (default value)                                                                     |

### Response {#post-a-long-video-response}

**Content-Type:** application/json

**Body**

| Data Name    | Data Type | Description                                                                                                   |
|--------------|-----------|---------------------------------------------------------------------------------------------------------------|
| code         | int       | 2000: created succeeded; <br/>4001: invalid api key; <br/>4002: created failed; <br/>4003: requests exceeded the limit; <br/>4004: unsupported video format; <br/>4005: invalid video url; <br/>4006: illegal parameter; <br/>4007: insufficient remaining time in the account |
| projectid    | string    | Used for polling short clips generation                                                                       |

## Step 3: Get short clips {#get-short-clips}

Vizard.ai offers two ways to get short clips. First, it can be obtained by polling. Second, the webhook address can be configured in the workbench. Once the webhook address is configured, Vizard.ai will send the short clips to the webhook address. Either way, you'll get the same content.
Please note: for webhook address, we currently only do one callback, please make sure your server is available, otherwise you will have to poll the clip results.

### Poll Request {#polling-short-clips-request}

**URL**

```python
https://elb-api.vizard.ai/hvizard-server-front/open-api/v1/project/query/{projectId}
```

**Method：**GET

**Headers**

| Name of Header | Value                    | Required |
|-----------------|--------------------------|----------|
| VIZARDAI_API_KEY | Vizard ai api key       | YES      |

**Parameters**

| Name | Data Type                  | Required |  Description   |
|-----------------|--------------------------|----------|-----|
| projectId | Long      | YES      |  Get the video export result by projectId. <br/>Please append it to https request url, behind ’/query/’. |

### Poll Response {#polling-short-clips-response}

**Content-Type**: application/json

**Body**

| Data Name | Data Type      | Description                                                                                                           |
|-----------|----------------|-----------------------------------------------------------------------------------------------------------------------|
| code      | int            | 1000: processing; <br/>2000: clipping succeeded; <br/>4001: invalid api key; 4002: clipping failed; <br/>4003: requests exceeded the limit; <br/>4004: unsupported video format; <br/>4005: invalid video url; <br/>4006: illegal parameter; <br/>4007: insufficient remaining time in the account |
| videos    | array          | A list of URLs for video clips. The video URL is valid for 3 hours. Please ensure that you complete the download within this time frame. If the 3-hour period expires, you can query again to obtain a new valid video URL. It is important to note that after 7 days, the video will be permanently deleted. |

**Video**

| Field Name     | Data Type | Description                   |
|----------------|-----------|-------------------------------|
| projectId      | Long      | The id of project.            |
| videoUrl       | string    | Video download URL.           |
| videoMsDuration| long      | Video duration in milliseconds.|
| title          | string    | Video title.                  |
| viralReason    | string    | AI clip reason.               |
| viralScore     | float     | AI clip score.                |
| transcript     | string    | Video text content.           |
| relatedTopic   | array     | Video related topics.         |


# Appendix

## Supported video languages

| Language Code | Language Name |
|---------------|---------------|
| ar            | Arabic        |
| nl            | Dutch         |
| en            | English       |
| fr            | French        |
| de            | German        |
| hi            | Hindi         |
| id            | Indonesian    |
| it            | Italian       |
| ja            | Japanese      |
| ko            | Korean        |
| zh            | Mandarin      |
| zh-TW         | Mandarin      |
| pt            | Portuguese    |
| Russian       | (No code provided) |
| es            | Spanish       |
| tr            | Turkish       |
| uk            | Ukrainian     |
| vi            | Vietnamese    |

## Supported file types 

mp4, 3gp, avi, mov

## Maximum video length 

360 mins

## Maximum file size

8GB

## Video storage

As long as you are subscribing

## Upload minutes

Same as the minutes in your plan

# Sample Code

## Python {#sample-code-python}

```python
import requests
import time

VIZARDAI_API_KEY = "YOUR_VIZARD_AI_API_KEY"

PROJECT_NAME = "api-vizard-sample"
VIDEO_URL = "https://cdn-docs.vizard.ai/openapi/sample.mp4"
VIDEO_FILE_EXT = "mp4"
LANG = "en"
PREFER_LENGTH = "[0]"
SUBTITLE_SWITCH = 1
HEADLINE_SWITCH = 1

headers = {
   "Content-Type": "application/json",
   "VIZARDAI_API_KEY": VIZARDAI_API_KEY
}

def create_project():
   create_data = {
       'projectName': PROJECT_NAME,
       'videoUrl':  VIDEO_URL,
       'ext':  VIDEO_FILE_EXT,
       'lang': LANG,
       'preferLength': PREFER_LENGTH,
       'subtitleSwitch': SUBTITLE_SWITCH,
       'headlineSwitch': HEADLINE_SWITCH,
   }
   create_url = "https://elb-api.vizard.ai/hvizard-server-front/open-api/v1/project/create"

   print('creating project')
   response = requests.post(create_url, headers=headers, json=create_data)
   create_result = response.json()
   if response.status_code == 200:
       print(create_result)
       if create_result["code"] == 2000:
           project_id = create_result["projectId"]
       else:
           print("create error:", create_result)
           project_id = ""
   else:
       print('create request failed: ', create_result)
       project_id = ""
   return project_id

def query_videos(project_id):
   query_url = "https://elb-api.vizard.ai/hvizard-server-front/open-api/v1/project/query/" + str(project_id)

   while (True):
       print('querying videos')
       response = requests.get(query_url, headers=headers)
       if response.status_code == 200:
           query_result = response.json()
           if query_result["code"] == 2000:
               print('clipping succeeded.')
               print(query_result)
               break
           elif query_result["code"] == 1000:
               time.sleep(5)
               continue
           else:
               print(query_result)
               print("clipping error:")
               if 'message' in query_result:
                   print(query_result['message'])
               break
       else:
           print('query request failed: ', response.json)
           break

def main():
   project_id = create_project()
   if (project_id != ""):
       query_videos(project_id)

if __name__ == "__main__":
   main()
```
