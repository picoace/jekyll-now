---
layout: post
title: WEBTOB-http.m.sample
---

# WEBTOB-http.m.sample

## 1. WEBTOB 환경파일 샘플

### 1.1. ssl 테스트 명령
ssl 테스트 명령	
```
wbssl ciphers -v
wbssl s_client -connect www.daum.net:443

```

### 1.2. 환경파일 샘플
환경파일 샘플
```
*DOMAIN
webtob1

*NODE
toronto		    
                WEBTOBDIR="/home/tmax/webtob4191",
                SHMKEY = 54000,
                DOCROOT="/home/tmax/webtob4191/docs",
                LISTEN="toronto:3114",
                HTH = 1,
                Group = "dba",
                User = "tmax",
                NODENAME = "$(NODENAME)",
                ERRORDOCUMENT = "503",
                SERVICEORDER = "ext,uri",
                IPCPERM=0777,
                LIMITREQUESTBODY=10240000,
                JSVPORT = 9900,
                LOGGING = "log1",
                ERRORLOG = "log2",
                SYSLOG = "log3"

*VHOST
examples          
                HOSTNAME="www.examples.co.kr",
                LISTEN="toronto:80",
                SERVICEORDER = "ext,uri",
                PORT="80",
                DOCROOT="/home/tmax/app/homepage/webapp"


example_ssl     
                DOCROOT="/home/tmax/app/homepage/webapp",
                PORT="443",
                SSLFLAG   = Y,
                SSLNAME   = "examplesssl",
                HOSTNAME  = "www.examples.or.kr",
                HOSTALIAS = "www.examples.or.kr,examples.or.kr"


*SSL
examplesssl     CertificateFile = "/home/tmax/webtob4191/ssl/examples_ssl/ssl/examples.or.kr.crt",
                CertificateKeyFile = "/home/tmax/webtob4191/ssl/examples_ssl/ssl/examples.or.kr.key",
                CACertificateFile = "/home/tmax/webtob4191/ssl/examples_ssl/ssl/ca-bundle.crt"
                ## Protocols = "SSLv3, TLSv1, TLSv1.1, TLSv1.2",
                ## RequiredCiphers = "HIGH:MEDIUM:!SSLv2:!PSK:!SRP:!ADH:!AECDH:!EXP:!RC4:!IDEA:!3DES"


*SVRGROUP
htmlg           SVRTYPE = HTML
jsvg            SVRTYPE = JSV

*SERVER
html            SVGNAME = htmlg, MinProc = 100, MaxProc = 100
examples        SVGNAME = jsvg, MinProc = 100, MaxProc = 100, svrchktime=60

*URI
#### Default WAS Uri
examples_root_uri     Uri = "/",   Svrtype = JSV, svrname="examples", vhostname="examples, example_ssl"

*ALIAS
examples_example      URI = "/examples/", RealPath = "/home/tmax/jeus7004/domains/jeus_domain/servers/server1/.workspace/deployed/examples/examples_ear___/examples_war___/"
examples_root         URI = "/", RealPath = "/home/tmax/jeus7004/domains/jeus_domain/servers/server1/.workspace/deployed/examples/examples_ear___/welcome_war___/"


*LOGGING
log1            Format = "DEFAULT", FileName = "/home/tmax/webtob4191/log/access.log_%Y%%M%%D%",
                        Option = "sync,env=!Image"
log2            Format = "ERROR", FileName = "/home/tmax/webtob4191/log/error.log_%Y%%M%%D%",
                        Option = "sync,env=!Image"
log3            Format = "SYSLOG", FileName = "/home/tmax/webtob4191/log/system.log_%Y%%M%%D%",
                        Option = "sync,env=!Image"


*ERRORDOCUMENT
503             status = 503,
                url = "/503.html"


*EXT
jsp     Options = "UnSet"
cab    MimeType = "x-application/octet-stream", SvrType = HTML
ocx     MimeType = "text/plain", SvrType = HTML
js      MimeType = "application/x-javascript", SvrType = HTML
css     MimeType = "text/css", SvrType = HTML
htm     MimeType = "text/html", SvrType = HTML
html    MimeType = "text/html", SvrType = HTML
xml     MimeType = "text/html", SvrType = HTML
mht     MimeType = "text/plain", SvrType = HTML
mhtml   MimeType = "text/plain", SvrType = HTML
gul     MimeType = "application/gul", SvrType = HTML
hwp     MimeType = "application/x-hwp", SvrType = HTML
pdf     MimeType="application/pdf", SvrType=HTML
zip     MimeType="application/zip", SvrType=HTML
jar     MimeType="application/x-java-archive", SvrType=HTML
exe     MimeType="application/octet-stream", SvrType=HTML
csv     MimeType="application/octet-stream", SvrType=HTML
wml     MimeType="application/vnd.wap.wml", SvrType=HTML
wmls    MimeType="application/vnd.wap.wmlscript", SvrType=HTML
wmlc    MimeType="application/vnd.wap.wmlc", SvrType=HTML
wmlsc   MimeType="application/vnd.wap.wmlscript", SvrType=HTML
wbmp    MimeType="application/vnd.wap.wbmp", SvrType=HTML
bin     MimeType="application/octet-stream",SvrType=HTML
asd     MimeType="application/astound",SvrType=HTML
asn     MimeType="application/astound",SvrType=HTML
lcc     MimeType="application/fastman",SvrType=HTML
ser     MimeType="application/java-serialized-object",SvrType=HTML
class   MimeType="application/java-vm",SvrType=HTML
hqx     MimeType="application/mac-binhex40",SvrType=HTML
sit     MimeType="application/x-stuffit",SvrType=HTML
mbd     MimeType="application/mbedlet",SvrType=HTML
doc     MimeType="application/msword",SvrType=HTML
wiz     MimeType="application/msword",SvrType=HTML
rtf     MimeType="application/msword",SvrType=HTML
oda     MimeType="application/oda",SvrType=HTML
ai      MimeType="application/postscript",SvrType=HTML
eps     MimeType="application/postscript",SvrType=HTML
ps      MimeType="application/postscript",SvrType=HTML
smp     MimeType="application/studiom",SvrType=HTML
tbt     MimeType="application/timbuktu",SvrType=HTML
xls     MimeType="application/vnd.ms-excel",SvrType=HTML
xlw     MimeType="application/vnd.ms-excel",SvrType=HTML
xla     MimeType="application/vnd.ms-excel",SvrType=HTML
xlc     MimeType="application/vnd.ms-excel",SvrType=HTML
xlm     MimeType="application/vnd.ms-excel",SvrType=HTML
xlt     MimeType="application/vnd.ms-excel",SvrType=HTML
ppt     MimeType="application/vnd.ms-powerpoint",SvrType=HTML
pps     MimeType="application/vnd.ms-powerpoint",SvrType=HTML
pot     MimeType="application/vnd.ms-powerpoint",SvrType=HTML
mpp     MimeType="application/vnd.ms-project",SvrType=HTML
hlp     MimeType="application/winhlp",SvrType=HTML
jsu     MimeType="application/x-javascript;charset=UTF-8",SvrType=HTML
aim     MimeType="application/x-aim",SvrType=HTML
asp     MimeType="application/x-asap",SvrType=HTML
csh     MimeType="application/x-csh",SvrType=HTML
dot     MimeType="application/x-dot",SvrType=HTML
dvi     MimeType="application/x-dvi",SvrType=HTML
etc     MimeType="application/x-earthtime",SvrType=HTML
evy     MimeType="application/x-envoy",SvrType=HTML
gtar    MimeType="application/x-gtar",SvrType=HTML
cpio    MimeType="application/x-cpio",SvrType=HTML
hdf     MimeType="application/x-hdf",SvrType=HTML
latex   MimeType="application/x-latex",SvrType=HTML
jsc     MimeType="application/x-javascript-config",SvrType=HTML
fm      MimeType="application/x-maker",SvrType=HTML
mif     MimeType="application/x-mif",SvrType=HTML
mi      MimeType="application/x-mif",SvrType=HTML
mocha   MimeType="application/x-mocha",SvrType=HTML
moc     MimeType="application/x-mocha",SvrType=HTML
mdb     MimeType="application/x-msaccess",SvrType=HTML
crd     MimeType="application/x-mscardfile",SvrType=HTML
clp     MimeType="application/x-msclip",SvrType=HTML
m13     MimeType="application/x-msmediaview",SvrType=HTML
m14     MimeType="application/x-msmediaview",SvrType=HTML
wmf     MimeType="application/x-msmetafile",SvrType=HTML
mny     MimeType="application/x-msmoney",SvrType=HTML
pub     MimeType="application/x-mspublisher",SvrType=HTML
scd     MimeType="application/x-msschedule",SvrType=HTML
trm     MimeType="application/x-msterminal",SvrType=HTML
wri     MimeType="application/x-mswrite",SvrType=HTML
ins     MimeType="application/x-NET-Install",SvrType=HTML
nc      MimeType="application/x-netcdf",SvrType=HTML
cdf     MimeType="application/x-netcdf",SvrType=HTML
proxy   MimeType="application/x-ns-proxy-autoconfig",SvrType=HTML
slc     MimeType="application/x-salsa",SvrType=HTML
sh      MimeType="application/x-sh",SvrType=HTML
shar    MimeType="application/x-shar",SvrType=HTML
spr     MimeType="application/x-sprite",SvrType=HTML
sprite  MimeType="application/x-sprite",SvrType=HTML
tar     MimeType="application/x-tar",SvrType=HTML
tcl     MimeType="application/x-tcl",SvrType=HTML
pl      MimeType="application/x-perl",SvrType=HTML
tex     MimeType="application/x-tex",SvrType=HTML
texinfo MimeType="application/x-texinfo",SvrType=HTML
texi    MimeType="application/x-texinfo",SvrType=HTML
tbp     MimeType="application/x-timbuktu",SvrType=HTML
tki     MimeType="application/x-tkined",SvrType=HTML
tkined  MimeType="application/x-tkined",SvrType=HTML
man     MimeType="application/x-troff-man",SvrType=HTML
me      MimeType="application/x-troff-me",SvrType=HTML
ms      MimeType="application/x-troff-ms",SvrType=HTML
t       MimeType="application/x-troff",SvrType=HTML
tr      MimeType="application/x-troff",SvrType=HTML
roff    MimeType="application/x-troff",SvrType=HTML
src     MimeType="application/x-wais-source",SvrType=HTML
enc     MimeType="application/pre-encrypted",SvrType=HTML
crl     MimeType="application/x-pkcs7-crl",SvrType=HTML
ckl     MimeType="application/x-fortezza-ckl",SvrType=HTML
au      MimeType="audio/basic",SvrType=HTML
snd     MimeType="audio/basic",SvrType=HTML
es      MimeType="audio/echospeech",SvrType=HTML
esl     MimeType="audio/echospeech",SvrType=HTML
midi    MimeType="audio/midi",SvrType=HTML
mid     MimeType="audio/midi",SvrType=HTML
aif     MimeType="audio/x-aiff",SvrType=HTML
aiff    MimeType="audio/x-aiff",SvrType=HTML
aifc    MimeType="audio/x-aiff",SvrType=HTML
#midi   MimeType="audio/x-midi",SvrType=HTML
#mid    MimeType="audio/x-midi",SvrType=HTMLW
wav     MimeType="audio/x-wav",SvrType=HTML
ra      MimeType="audio/x-pn-realaudio",SvrType=HTML
ram     MimeType="audio/x-pn-realaudio",SvrType=HTML
pac     MimeType="audio/x-pac",SvrType=HTML
pae     MimeType="audio/x-epac",SvrType=HTML
lam     MimeType="audio/x-liveaudio",SvrType=HTML
dwf     MimeType="drawing/x-dwf",SvrType=HTML
fif     MimeType="image/fif",SvrType=HTML
ico     MimeType="image/x-icon",SvrType=HTML
gif     MimeType="image/gif",SvrType=HTML
ief     MimeType="image/ief",SvrType=HTML
ifs     MimeType="image/ifs",SvrType=HTML
jpeg    MimeType="image/jpeg",SvrType=HTML
jpg     MimeType="image/jpeg",SvrType=HTML
jpe     MimeType="image/jpeg",SvrType=HTML
jfif    MimeType="image/jpeg",SvrType=HTML
pjpeg   MimeType="image/jpeg",SvrType=HTML
pjp     MimeType="image/jpeg",SvrType=HTML
png     MimeType="image/png",SvrType=HTML
tiff    MimeType="image/tiff",SvrType=HTML
tif     MimeType="image/tiff",SvrType=HTML
dwg     MimeType="image/vnd",SvrType=HTML
svf     MimeType="image/vnd",SvrType=HTML
wi      MimeType="image/wavelet",SvrType=HTML
bmp     MimeType="image/bmp",SvrType=HTML
pcd     MimeType="image/x-photo-cd",SvrType=HTML
ras     MimeType="image/x-cmu-raster",SvrType=HTML
pnm     MimeType="image/x-portable-anymap",SvrType=HTML
pbm     MimeType="image/x-portable-bitmap",SvrType=HTML
pgm     MimeType="image/x-portable-graymap",SvrType=HTML
ppm     MimeType="image/x-portable-pixmap",SvrType=HTML
rgb     MimeType="image/x-rgb",SvrType=HTML
xbm     MimeType="image/x-xbitmap",SvrType=HTML
xpm     MimeType="image/x-xpixmap",SvrType=HTML
xwd     MimeType="image/x-xwindowdump",SvrType=HTML
txt     MimeType="text/plain",SvrType=HTML
rtx     MimeType="text/richtext",SvrType=HTML
tsv     MimeType="text/tab-separated-values",SvrType=HTML
etx     MimeType="text/x-setext",SvrType=HTML
talk    MimeType="text/x-speech",SvrType=HTML
xul     MimeType="text/xul",SvrType=HTML
fvi     MimeType="video/isivideo",SvrType=HTML
mpeg    MimeType="video/mpeg",SvrType=HTML
mpg     MimeType="video/mpeg",SvrType=HTML
mpe     MimeType="video/mpeg",SvrType=HTML
mpv     MimeType="video/mpeg",SvrType=HTML
vbs     MimeType="video/mpeg",SvrType=HTML
mpegv   MimeType="video/mpeg",SvrType=HTML
mpv2    MimeType="video/x-mpeg2",SvrType=HTML
mp2v    MimeType="video/x-mpeg2",SvrType=HTML
avi     MimeType="video/msvideo",SvrType=HTML
qt      MimeType="video/quicktime",SvrType=HTML
mov     MimeType="video/quicktime",SvrType=HTML
moov    MimeType="video/quicktime",SvrType=HTML
viv     MimeType="video/vivo",SvrType=HTML
vivo    MimeType="video/vivo",SvrType=HTML
wv      MimeType="video/wavelet",SvrType=HTML
movie   MimeType="video/x-sgi-movie",SvrType=HTML
htc     MimeType = "text/x-component", SvrType = HTML
svr     MimeType="x-world/x-svr",SvrType=HTML
wrl     MimeType="x-world/x-vrml",SvrType=HTML
vrt     MimeType="x-world/x-vrt",SvrType=HTML
ice     MimeType="x-conference/x-cooltalk",SvrType=HTML
gz      MimeType="enc=x-gzip",SvrType=HTML
z       MimeType="enc=x-compress",SvrType=HTML
uu      MimeType="enc=x-uuencode",SvrType=HTML
uue     MimeType="enc=x-uuencode",SvrType=HTML
map     MimeType="magnus-internal/imagemap",SvrType=HTML
shtml   MimeType="magnus-internal/parsed-html",SvrType=HTML
bat     MimeType="magnus-internal/cgi",SvrType=HTML
cacert  MimeType="application/x-x509-ca-cert",SvrType=HTML
scert   MimeType="application/x-x509-server-cert",SvrType=HTML
ucert   MimeType="application/x-x509-user-cert",SvrType=HTML
ecert   MimeType="application/x-x509-email-cert",SvrType=HTML
asf MimeType = "video/x-msvideo", SvrType = HTML 
wmv MimeType = "video/x-msvideo", SvrType = HTML 
swf             MimeType = "application/x-shockwave-flash", SvrType = HTML
pkg     MimeType="application/x-newton-compatible",SvrType=HTML
deb     MimeType="application/x-devian-package",SvrType=HTML
rpm     MimeType="application/x-rpm",SvrType=HTML
dl_     MimeType="application/octet-stream",SvrType=HTML
ex_     MimeType="application/octet-stream",SvrType=HTML
vcs     MimeType="text/x-vcalendar",SvrType=HTML
dist    MimeType="application/octet-stream", SvrType=HTML
woff    MimeType="application/font-woff",SvrType=HTML
svg    MimeType="image/svg+xml",SvrType=HTML         
ttf    MimeType="application/x-font-ttf",SvrType=HTML
otf    MimeType="application/x-font-opentype",SvrType=HTML    
eot    MimeType="application/vnd.ms-fontobject",SvrType=HTML
xpi    MimeType="application/x-xpinstall",SvrType=HTML
dmg     MimeType="application/x-apple-diskimage",SvrType=HTML


```


