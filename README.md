# Python Wrapper Class for Tesseract
##### (Linux & Mac OS X & Windows)
Python-tesseract is a wrapper class for Tesseract OCR that allows any conventional image files (JPG, GIF ,PNG , TIFF and etc) to be read and decoded into readable languages. No temporary file will be created during the OCR processing.
___
### Setup
1. Set `PATH`: e.g. `PATH=%PATH%;C:\PYTHON27` [Details](http://pythoncentral.io/add-python-to-path-python-is-not-recognized-as-an-internal-or-external-command/)
2. Set `c:\python27\python.exe` to be compatible to Windows 7 even though you are using windows 7. Otherwise the program might crash during runtime. [Details](python-tesseract/files/pythonCompatible.png)
3. Download and install [Numpy](http://www.numpy.org/) and [python-opencv](https://opencv-python-tutroals.readthedocs.org/en/latest/py_tutorials/py_setup/py_table_of_contents_setup/py_table_of_contents_setup.html)
4. Unzip the [sample code](https://github.com/Arengorn/python-tesseract/blob/master/files/test-slim.7z) and keep your fingers crossed.
5. `python -u test.py` (It's always safer to run Python in unbuffered mode, specially on Windows XP)
___
### Examples
```python
import tesseract
api = tesseract.TessBaseAPI()
api.SetOutputName("outputName");
api.Init(".","eng",tesseract.OEM_DEFAULT)
api.SetPageSegMode(tesseract.PSM_AUTO)
mImgFile = "eurotext.jpg"
pixImage=tesseract.pixRead(mImgFile)
api.SetImage(pixImage)
outText=api.GetUTF8Text()
print("OCR output:\n%s"%outText);
api.End()```
```python
import tesseract
api = tesseract.TessBaseAPI()
api.Init(".","eng",tesseract.OEM_DEFAULT)
api.SetVariable("tessedit_char_whitelist", "0123456789abcdefghijklmnopqrstuvwxyz")
api.SetPageSegMode(tesseract.PSM_AUTO)
mImgFile = "eurotext.jpg"
mBuffer=open(mImgFile,"rb").read()
result = tesseract.ProcessPagesBuffer(mBuffer,len(mBuffer),api)
print "result(ProcessPagesBuffer)=",result
api.End()```
```python
import cv2.cv as cv
import tesseract
api = tesseract.TessBaseAPI()
api.Init(".","eng",tesseract.OEM_DEFAULT)
api.SetPageSegMode(tesseract.PSM_AUTO)
image=cv.LoadImage("eurotext.jpg", cv.CV_LOAD_IMAGE_GRAYSCALE)
tesseract.SetCvImage(image,api)
text=api.GetUTF8Text()
conf=api.MeanTextConf()
print text
api.End()
```
```python
import tesseract
import cv2
import cv2.cv as cv
image0=cv2.imread("p.bmp")
#### you may need to thicken the border in order to make tesseract feel happy to ocr your image #####
offset=20
height,width,channel = image0.shape
image1=cv2.copyMakeBorder(image0,offset,offset,offset,offset,cv2.BORDER_CONSTANT,value=(255,255,255))
#cv2.namedWindow("Test")
#cv2.imshow("Test", image1)
#cv2.waitKey(0)
#cv2.destroyWindow("Test")
#####################################################################################################
api = tesseract.TessBaseAPI()
api.Init(".","eng",tesseract.OEM_DEFAULT)
api.SetPageSegMode(tesseract.PSM_AUTO)
height1,width1,channel1=image1.shape
print image1.shape
print image1.dtype.itemsize
width_step = width*image1.dtype.itemsize
print width_step
#method 1
iplimage = cv.CreateImageHeader((width1,height1), cv.IPL_DEPTH_8U, channel1)
cv.SetData(iplimage, image1.tostring(),image1.dtype.itemsize * channel1 * (width1))
tesseract.SetCvImage(iplimage,api)
text=api.GetUTF8Text()
conf=api.MeanTextConf()
image=None
print "..............."
print "Ocred Text: %s"%text
print "Cofidence Level: %d %%"%conf
#method 2:
cvmat_image=cv.fromarray(image1)
iplimage =cv.GetImage(cvmat_image)
print iplimage
tesseract.SetCvImage(iplimage,api)
#api.SetImage(m_any,width,height,channel1)
text=api.GetUTF8Text()
conf=api.MeanTextConf()
image=None
print "..............."
print "Ocred Text: %s"%text
print "Cofidence Level: %d %%"%conf
api.End()
```
```python
import tesseract
import cv2
import cv2.cv as cv
image0=cv2.imread("eurotext.jpg")
offset=20
height,width,channel = image0.shape
image1=cv2.copyMakeBorder(image0,offset,offset,offset,offset,cv2.BORDER_CONSTANT,value=(255,255,255))
api = tesseract.TessBaseAPI()
api.Init(".","eng",tesseract.OEM_DEFAULT)
api.SetPageSegMode(tesseract.PSM_AUTO)
height1,width1,channel1=image1.shape
print image1.shape
print image1.dtype.itemsize
width_step = width*image1.dtype.itemsize
print width_step
iplimage = cv.CreateImageHeader((width1,height1), cv.IPL_DEPTH_8U, channel1)
cv.SetData(iplimage, image1.tostring(),image1.dtype.itemsize * channel1 * (width1))
tesseract.SetCvImage(iplimage,api)
api.Recognize(None)
ri=api.GetIterator()
level=tesseract.RIL_WORD
count=0
while (ri):
        word = ri.GetUTF8Text(level)
        conf = ri.Confidence(level)
        print "[%03d]:\tword(confidence)=%s(%.2f%%)"%(count,word,conf)
        #ri.BoundingBox(level,x1,y1,x2,y2)
        count+=1
        if not ri.Next(level):
                break
iplimage=None
api.End()
```
