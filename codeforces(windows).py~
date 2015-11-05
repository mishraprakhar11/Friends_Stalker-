#If you system lacks any module, install it by help of google.
#further details in blog.
import sys, os
from sys import argv
from win32api import *
from win32gui import *
import urllib2
import json
import time
import win32con
import struct

script , friends = argv
txt = open(friends).read()
 
#make sure that friends.txt file has EXACT same spelling of handles(space separated) of your friends.
 
#li will be a list containing all you friends. I suggest to add your name 
#too,to get updated with your system test results along with your friends.
li = txt.split()
 
#notifier for windows.
class WindowsBalloonTip:
    def __init__(self, title, msg):
        message_map = {
                win32con.WM_DESTROY: self.OnDestroy,
        }
        # Register the Window class.
        wc = WNDCLASS()
        hinst = wc.hInstance = GetModuleHandle(None)
        wc.lpszClassName = "PythonTaskbar"
        wc.lpfnWndProc = message_map # could also specify a wndproc.
        classAtom = RegisterClass(wc)
        # Create the Window.
        style = win32con.WS_OVERLAPPED | win32con.WS_SYSMENU
        self.hwnd = CreateWindow( classAtom, "Taskbar", style, \
                0, 0, win32con.CW_USEDEFAULT, win32con.CW_USEDEFAULT, \
                0, 0, hinst, None)
        UpdateWindow(self.hwnd)
        iconPathName = os.path.abspath(os.path.join( sys.path[0], "balloontip.ico" ))
        icon_flags = win32con.LR_LOADFROMFILE | win32con.LR_DEFAULTSIZE
        try:
           hicon = LoadImage(hinst, iconPathName, \
                    win32con.IMAGE_ICON, 0, 0, icon_flags)
        except:
          hicon = LoadIcon(0, win32con.IDI_APPLICATION)
        flags = NIF_ICON | NIF_MESSAGE | NIF_TIP
        nid = (self.hwnd, 0, flags, win32con.WM_USER+20, hicon, "tooltip")
        Shell_NotifyIcon(NIM_ADD, nid)
        Shell_NotifyIcon(NIM_MODIFY, \
                         (self.hwnd, 0, NIF_INFO, win32con.WM_USER+20,\
                          hicon, "Balloon  tooltip",msg,200,title))
        # self.show_balloon(title, msg)
        time.sleep(10)
        DestroyWindow(self.hwnd)
        classAtom = UnregisterClass(classAtom, hinst)
    def OnDestroy(self, hwnd, msg, wparam, lparam):
        nid = (self.hwnd, 0)
        Shell_NotifyIcon(NIM_DELETE, nid)
        PostQuitMessage(0) # Terminate the app.

def balloon_tip(title, msg):
    w=WindowsBalloonTip(title, msg)
 
def main():
    rem = []
 
    #flag will cause the first iteration go without any alerts.This will cause the code detect the
    #solution after it starts.Otherwise you will have to cancel all alerts of first iteration (sucks).
    
    #set flag = True to check if notifications are working right in your system.Turn False after!
    flag = False
    print "CONSOLE:"
    
    while 1:
        for user in li:
            #preparing url for a particular friend.Below three lines are just json and api stuff.
            #I have set count = 15 (code will track last 15 solutions of user).
            url = 'http://codeforces.com/api/user.status?handle=' + user +'&from=1&count=15'
            json_obj = urllib2.urlopen(url)
            data = json.load(json_obj)
 
            #iterate through all x solutions of user.
            for pick in data['result']:
                #Considering Accepted solutions.
                if pick['verdict'] == "OK":
 
                    #This will be used for system tests.
                    if pick['testset'] == "TESTS": 
                        myobj = pick['problem']
                        #temp is an attempt to make a submission uniquely enter in rem. (through SubmissionID) 
                        temp = str(pick['id']) + pick['verdict']
 
                        if temp not in rem:
                            rem.append(temp)
                            create = user + "- |" + myobj['index'] + "|" + myobj['name'] 
                            if flag == True:
                                balloon_tip("Accepted!!", create)
                                print "[Accepted!]"  , create
 
                    #this will be used for pretests 
                    elif pick['testset']  == "PRETESTS":
                        myobj = pick['problem']
                        temp =  pick['id']
                        if temp not in rem:
                            rem.append(temp)
                            create = user + "- |" + myobj['index'] + "|" + myobj['name'] 
                            balloon_tip("Pretest-passed!!", create)
                            print "[pretest-passed]"  , create
 
                #this will run when non Accepted solutions.Comment everything 
                #in below else if you do not wish to see wrong submissions.
                elif pick['verdict'] != "TESTING":
                    myobj = pick['problem']
                    temp  = str(pick['id']) + pick['verdict']
 
                    if temp not in rem:
                        rem.append(temp)
                        create = user + "- |" + myobj['index'] + "|" + myobj['name']
                        if flag == True:
                            balloon_tip(pick['verdict'], create)
                            print "[" + pick['verdict'] + "]", create
                                                                         
        #we have cached all previous solutions at rem list.from now on it  
        #will detect only new submitted solutions.Used only at first iteration.
        flag = True
 
        #insert time according to you patience.Unit is seconds.
        time.sleep(180)
 
if __name__ == '__main__':
    main()
