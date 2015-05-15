---
layout: page
title: Controller Assignment Solution
modified: 05-14-2015
comments: true
---

### HeartBeat Response

The controller for this question is really simple - it's just testing whether you understand the basic mechanism of controllers. The reference source code is:

{% highlight python %}
@csrf_exempt
def heart_beat(request):
    if request.method != 'GET':
        return HttpResponseNotAllowed(['GET'])
    username = request.REQUEST.get('username', None)
    if username is None:
        return HttpResponseBadRequest('username required')

    return HttpResponse('<b>%s</b>' % username)

{% endhighlight %}

One thing to point out is since we want to support POST, we use <code>@csrf_exempt</code> to work around Django's check. 

### File Manipulation

**CreateFile controller**

The controller accepts a file stream as data in the request, store that file somewhere on the server, and return to client an id which can be used to retrieve this file.

First, we need to read the upload file stream from the request object

{% highlight python %}
fs = request.FILES.get('file', None)
if fs is None:
    return HttpResponseBadRequest('file is required')
{% endhighlight %}

Then, we create a unique id for this file, and use that id to determine where we'll save the file on the server's file system

{% highlight python %}
uid = str(uuid.uuid1()).replace('-', '')
savepath = os.path.join(MEDIA_ROOT, 'practise', uid)
{% endhighlight %}

Note that <code>MEDIA_ROOT</code> is imported by <code>from web_dev_tutorial.settings import MEDIA_ROOT</code> at the beginning of the <code>practise.py</code>. And in <code>settings.py</code> you can set the variable with something like

{% highlight python %}
BASE_DIR = os.path.dirname(os.path.dirname(__file__))
MEDIA_ROOT = os.path.join(BASE_DIR, 'upload')
{% endhighlight %}

Finally, we save the file, and responses to client with the id

{% highlight python %}
data = fs.read()
out_fs = open(savepath, 'wb')
out_fs.write(data)
out_fs.close()

return HttpResponse(json.dumps({'id': uid}))
{% endhighlight %}


**GetFile controller**

This controller supports retrieval of files. It expects an id as parameter in the request, use that id to locate the file, and return the binary file stream of that file.

First we locate the file with the given id. Note that this controller should only support **GET**

{% highlight python %}
if request.method != 'GET':
    return HttpResponseNotAllowed(['GET'])
savepath = os.path.join(MEDIA_ROOT, 'practise', file_id)
if not os.path.isfile(savepath):
    return HttpResponseNotFound('Invalid id')
{% endhighlight %}

Then, we just create a http response that encapsulates the binary stream of the target file

{% highlight python %}
read_fs = open(savepath, 'rb')
response = HttpResponse(read_fs)
response['Content-Disposition'] = 'attachment; filename=' + \
	os.path.split(savepath)[1]
return response
{% endhighlight %}

**Putting everything together**

{% highlight python %}
@csrf_exempt
def create_file(request):
    fs = request.FILES.get('file', None)
    if fs is None:
        return HttpResponseBadRequest('file is required')

    uid = str(uuid.uuid1()).replace('-', '')
    savepath = os.path.join(MEDIA_ROOT, 'practise', uid)
    data = fs.read()

    out_fs = open(savepath, 'wb')
    out_fs.write(data)
    out_fs.close()

    return HttpResponse(json.dumps({'id': uid}))


@csrf_exempt
def get_file(request, file_id):
    if request.method != 'GET':
        return HttpResponseNotAllowed(['GET'])

    savepath = os.path.join(MEDIA_ROOT, 'practise', file_id)
    if not os.path.isfile(savepath):
        return HttpResponseNotFound('Invalid id')

    read_fs = open(savepath, 'rb')

    response = HttpResponse(read_fs)
    response['Content-Disposition'] = 'attachment; filename=' \
    	+ os.path.split(savepath)[1]

    return response
{% endhighlight %}