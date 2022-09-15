# cartoonify
cartoonifying an image using openCV 
import cv2
import numpy as np
import easygui
from tkinter import *
from tkinter import messagebox 
import matplotlib.pyplot as plt
import os

def upload():
    ImagePath=easygui.fileopenbox()
    cartoonize(ImagePath)

def cartoonize(imgpath):
    img = cv2.imread(imgpath)
    img = cv2.resize(img, (640, 480))
    t1 = img
    cv2.imshow('10.png', img)
    cv2.waitKey()

    # Edges
    # gray filter
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    t2 = gray
    cv2.imshow('10.png', gray)
    cv2.waitKey()

    # median filter
    mfilter = cv2.medianBlur(gray, 7)
    t3 = mfilter
    cv2.imshow('10.png', mfilter)
    cv2.waitKey()

    # canny edge detection
    # gaussian filter
    gfilter = cv2.GaussianBlur(mfilter, (5, 5), 1.4)
    t4 = gfilter
    cv2.imshow('10.png', gfilter)
    cv2.waitKey()
    cannydetect = cv2.Canny(gfilter, 10, 50)
    t5 = cannydetect
    cv2.imshow('10.png', cannydetect)
    cv2.waitKey()

    # Dialation
    kernel = np.ones((2, 2), np.uint8)
    img_dilation = cv2.dilate(cannydetect, kernel, iterations=1)
    t6 = img_dilation
    cv2.imshow('10.png', img_dilation)
    cv2.waitKey()

    # Color
    # downsampling by 4
    imgd = cv2.resize(img, (img.shape[0]//4, img.shape[1]//4))

    # bilateral filtering
    for _ in range(14): 
        bfilter = cv2.bilateralFilter(imgd, 9, 9, 7)

    # resizing
    bfilter = cv2.resize(bfilter, (bfilter.shape[0]*4, bfilter.shape[1]*4))
    t7 = bfilter
    cv2.imshow('10.png', bfilter)
    cv2.waitKey()

    # medianfilter
    mfilter2 = cv2.medianBlur(bfilter, 7)
    t8 = mfilter2
    cv2.imshow('10.png', mfilter2)
    cv2.waitKey()

    img_edge = cannydetect
    img_edge = 255 - img_edge
    img_color = mfilter2
    (x,y,z) = img_color.shape 
    img_edge = cv2.resize(img_edge,(y,x)) 
    img_edge = cv2.cvtColor(img_edge, cv2.COLOR_GRAY2RGB) 
    cv2.imwrite("edge.png",img_edge) 
    res = cv2.bitwise_and(img_color, img_edge)
    cv2.imshow('10.png', res)
    cv2.waitKey()

    save1=Button(top,text="Save cartoon image",command=lambda: save(res, imgpath),padx=30,pady=5)
    save1.configure(background='#364156', foreground='white',font=('calibri',10,'bold'))
    save1.pack(side=TOP,pady=50)

def save(ReSized6, ImagePath):
    newName="cartoonified_Image"
    path1 = os.path.dirname(ImagePath)
    extension=os.path.splitext(ImagePath)[1]
    path = os.path.join(path1, newName+extension)
    cv2.imwrite(path, ReSized6)
    I = "Image saved by name " + newName +" at "+ path
    messagebox.showinfo(title=None, message=I)

top=Tk()
top.geometry('400x400')
top.title('Cartoonify Your Image !')
top.configure(background='white')
label=Label(top,background='#CDCDCD', font=('calibri',20,'bold'))
upload=Button(top,text="Cartoonify an Image",command=upload,padx=10,pady=5)
upload.configure(background='#364156', foreground='white',font=('calibri',10,'bold'))
upload.pack(side=TOP,pady=50)
top.mainloop()
