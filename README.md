# c-
c语言代码
VC视频教程笔记！
第一课
1.MFC生成的C++源文件中都有StdAfx.h，此文件包含了常用的AFX函数的声明，其中有afxwin.h,此文件包含了CRECT,CPoint，CWnd等许多类及其方法的声明。
2.Project->Setting->Debug可以加入命令行参数。
3.在SDK中要加入"windows.h"和stdio.h。因为LoadCursor,MessageBox等函数的声明在这个文件中。
4.创建一个完整的窗口的四个步骤SDK，1设计窗口类，2注册窗口类，3创建窗口，4显示窗口
5.函数名可以代表函数代码的首地址，即可作为函数指针。
6.要查看VC数据类型，可以在MSDN中输入“BOOL”然后选择“DATA TYPE”。
7.atof将字符串转化为float,atoi将字符串转化为int型。
8.所有从CWnd类派生的类都有m_hWnd句柄。
9.变量的生命周期：可以认为出了包含它的大括号，这个变量的生命周期结束。所以全局变量的声明位于所有大括号之外。但是用new声明的变量和用static声明的变量除外。
10.SDK示范程序，见下面。
11.sprintf格式化字符，其头文件为stdio.h,在MFC中格式化字符用CString.Format
12.GetDC()与ReleaseDC()要成对使用，否则会内存泄漏。同样，BeginPaint()与EndPaint()。
13.GetStockObject()得到画笔、画刷、字体、调色板的句柄，使用时必须用类型转换。
14.什么时候用NULL，什么时候用0.答，对指针赋值时用NULL，对变量赋值时用0.
15.什么是野指针？答：将指针指向的变量的内存释放后，此指针即变成野指针！如何避免野指针？答：将此指针指向NULL即可。p=NULL;
16.SDK代码流程：
#include "windows.h"//包含头文件LoadCursor,TextOut等函数
#include "stdio.h"//包含sprintf,printf等函数
LRESULT CALLBACK MyProc(...);//声明回调函数
int WINAPI WinMain()
{
WNDCLASS wndcls;//设计窗口类
wndcls.hcursor=LoadCursor();//初始化
....
RegisterClass(&wndcls);//注册窗口类
hwnd=CreateWindow(...);//创建窗口
ShowWindow(..);//显示窗口
UpdateWindow(..);
MSG msg;//定义消息结构体
while(GetMessage(...))//消息循环
{
...
}
return 0;
}
LRESULT CALLBACK MyProc(...)//实现回调函数
{
switch(uMsg)
{
case WM_CHAR:
break;
...
}
}
第2课
1.定义结构体和类时别忘记在最后加入"；"号！例如Class Point{int x;int y;};
2.#include <xxx.h>与#include "xxx.h"的区别：<>不查找运行时目录（从系统目录进行查找），""查找运行时目录（从当前目录进行查找）！
3.类的定义中，如果未指明成员类型，则缺省为private.而结构体中则缺省为public.
4.引用：引用经常用在函数的传参上。另外数值交换函数也经常用引用。例
change(int &x,int &y){int temp;temp=x;x=y;y=x}调用时即可以用 int a=3;int b=4;change(a,b);一般不用指针来作为参数进行数值交换。因为会引起歧义。
5.通常将类的定义放.h文件，而将其实现放在cpp文件中，别忘记了在cpp文件中#include "xxx.h"
6.如何防止类的重复定义？
用#ifndef Point_H_H
#define Point_H_H
class Point{};
#endif来防止
7.源文件cpp文件单独编译成obj文件。最后由链接器将与将要使用到的C++标准库类链接成exe文件，头文件不参加编译。所以在cpp文件中别忘记了加入#include "xxx.h"
8.函数的覆盖，在子类中重写父类的函数，此时采用早期绑定的方法。如果加入了virtual,则将采用迟绑定的技术，在运行时根据对象的类型确定调用哪一个函数。此迟绑定技术是MFC的类的继承的精髓。(多态性)
9.强制类型转换。如果CFish从CAnimal派生而来。则可以将鱼的对象转换为CAnimal的对象，而反之则不行。从现实中理解也是正常的，鱼可以是动物，而动物却不是鱼。再如int可以强制转换成char型。而反之则出错。
第3课
1.在main或WinMain之前，全局变量已经被分配内存并初始化了。
2.在MFC中在WinMain之前有个theApp全局变量先被构造并被初始化，而由于子类构造函数执行前，其父类的构造函数先被执行，所以CTestApp的父类CWinAPP的构造函数先执行。产生了theApp对象后，在WinMain()中的指针*pThread和*pApp就有了内容。
3.MFC大致流程：
CTestApp theApp;//构造全局对象
WinMain()
{
AfxWinMain();//调用下面的函数
}
AfxWinMain()
{
pThread->Initinstance();//初始化工作和注册窗口类，窗口显示和更新
pThread->Run();//消息循环
}
而在BOOL CTestApp::InitInstance()中的代码
CSingleDocTemplate* pDocTemplate;
pDocTemplate = new CSingleDocTemplate(
  IDR_MAINFRAME,
  RUNTIME_CLASS(CTestDoc),
  RUNTIME_CLASS(CMainFrame),       // main SDI frame window
  RUNTIME_CLASS(CTestView));
AddDocTemplate(pDocTemplate);
完成了将这三个类关联起来的工作。
4.如何在单文档文件中显示一个CButton的对象？
在CMainFrame::OnCreate()中定义一个CButton的对象btn;然后调用btn.Create("维新",WS_DISABLED   |WS_CHILD | WS_VISIBLE | BS_AUTO3STATE,
  CRect(0,0,300,100),/*GetParent(),*/this,123);
注意点：
     (1).此处btn不能是局部变量，否则它的生命周期太短，将不能显示。
     (2).在create函数的第二个参数中加入WS_VISIBLE 参数才行。否则必须调用ShowWindow
也可以在view的OnCreate消息响应函数中加入
     (3).CButton类的定义头文件在afxwin.h中，而stdafx.h包含了afxwin.h,所以可以直接使用。因为MFC中的每一个类中都有#include "stdafx.h"的声明。
5.CWnd封装了窗口句柄m_hWnd。
第4课
1.在单文档中view挡在MainFrame的前面。此时如果编写针对MainFrame的mouseClick事件，将不会有反应。
2.消息响应会在3处修改代码，1处是在头文件中，
//{{AFX_MSG(CDrawView)
afx_msg void OnLButtonDown(UINT nFlags, CPoint point);
afx_msg void OnLButtonUp(UINT nFlags, CPoint point);
afx_msg void OnMouseMove(UINT nFlags, CPoint point);
//}}AFX_MSG
DECLARE_MESSAGE_MAP()
另一处是cpp文件的begin MessageMap和End MessageMap之间，
BEGIN_MESSAGE_MAP(CDrawView, CView)
//{{AFX_MSG_MAP(CDrawView)
ON_WM_LBUTTONDOWN()
ON_WM_LBUTTONUP()
ON_WM_MOUSEMOVE()
//}}AFX_MSG_MAP
// Standard printing commands
ON_COMMAND(ID_FILE_PRINT, CView::OnFilePrint)
ON_COMMAND(ID_FILE_PRINT_DIRECT, CView::OnFilePrint)
ON_COMMAND(ID_FILE_PRINT_PREVIEW, CView::OnFilePrintPreview)
END_MESSAGE_MAP()
最后是要有函数实现的代码。
void CDrawView::OnLButtonDown(UINT nFlags, CPoint point) 
{
// TOD Add your message handler code here and/or call default
m_ptOrigin=m_ptOld=point;
m_bDraw=TRUE;
CView::OnLButtonDown(nFlags, point);
}
3.画线：定义一个成员变量保存mouseDown的点m_Point
  1)API函数方法画线用HDC
  2)用CDC类成员函数画线。此时别忘记ReleaseDC
  3)用CClientDC
  4)用CWindowDC,用它甚至可以整个屏幕区域画线。
下面是上面4种方法的代码
/*HDC hdc;
hdc=::GetDC(m_hWnd);
MoveToEx(hdc,m_ptOrigin.x,m_ptOrigin.y,NULL);
LineTo(hdc,point.x,point.y);
::ReleaseDC(m_hWnd,hdc);必须成对使用。*/
 CDC *pDC=GetDC();
pDC->MoveTo(m_ptOrigin);
pDC->LineTo(point);
ReleaseDC(pDC);必须成对使用。*/
CClientDC dc(this);
/*CClientDC dc(GetParent());
dc.MoveTo(m_ptOrigin);
dc.LineTo(point);此处不需要ReleaseDC,因为CClientDC会自动释放DC*/
//CWindowDC dc(this);
//CWindowDC dc(GetParent());
/*CWindowDC dc(GetDesktopWindow());//此时可以在整个屏幕上画线。
dc.MoveTo(m_ptOrigin);
dc.LineTo(point);*/
/*CPen pen(PS_DOT,1,RGB(0,255,0));
CClientDC dc(this);
CPen *pOldPen=dc.SelectObject(&pen);
dc.MoveTo(m_ptOrigin);
dc.LineTo(point);
dc.SelectObject(pOldPen);*/
  5)用Bitmap填充所画的矩形。
CBitmap bitmap;
bitmap.LoadBitmap(IDB_BITMAP1);
CBrush brush(&bitmap);
CClientDC dc(this);
dc.FillRect(CRect(m_ptOrigin,point),&brush);
//CBRUSH::FromHandle是静态成员函数，所以可以用下面的方法调用。
CBrush *pBrush=CBrush::FromHandle((HBRUSH)GetStockObject(NULL_BRUSH));
CBrush *pOldBrush=dc.SelectObject(pBrush);
dc.Rectangle(CRect(m_ptOrigin,point));
dc.SelectObject(pOldBrush);
m_bDraw=FALSE;
  6)用其它颜色画线
CClientDC dc(this);
CPen pen(PS_SOLID,1,RGB(255,0,0));
CPen *pOldPen=dc.SelectObject(&pen);//选中红色画笔
if(m_bDraw==TRUE)
{
  dc.SetROP2(R2_BLACK);//设置绘画模式
  dc.MoveTo(m_ptOrigin);
  //dc.LineTo(point);
  dc.LineTo(m_ptOld);
  //dc.MoveTo(m_ptOrigin);
  dc.MoveTo(m_ptOld);
  dc.LineTo(point);
  //m_ptOrigin=point;
  m_ptOld=point;
}
dc.SelectObject(pOldPen);
4.MFC中隐式的包含了windows.h。为什么？
因为在AFXV_W32.h文件中：
// This is a part of the Microsoft Foundation Classes C++ library.
// Copyright (C) 1992-1998 Microsoft Corporation
// All rights reserved.
在AFXWIN.h中
// Note: WINDOWS.H already included from AFXV_W32.H
5.如何从句柄获得对象的指针？
答FromHandle
6.类的静态成员函数可以由类名直接调用，也可以由对象调用。可以认为静态成员函数并不属于某个对象，它属于类本身。程序运行伊始，即使没有实例化类的对象，静态成员函数和静态成员变量已然有其内存空间。静态成员函数不能访问非静态成员变量！静态成员变量必须在类的外部初始化。当然如果并不打算用到静态成员变量，此时你可以不初始它。
7.理解代码区，数据区，堆，栈！
请见下面的简介：
http://www.downcode.com/server/j_server/J_1010.Html
对于一个进程的内存空间而言，可以在逻辑上分成3个部份：代码区，静态数据区和动态数据区。动态数据区一般就是“堆栈”。“栈(stack)”和“堆(heap)”是两种不同的动态数据区，栈是一种线性结构，堆是一种链式结构。进程的每个线程都有私有的“栈”，所以每个线程虽然代码一样，但本地变量的数据都是互不干扰。一个堆栈可以通过“基地址”和“栈顶”地址来描述。全局变量和静态变量分配在静态数据区，本地变量分配在动态数据区，即堆栈中。程序通过堆栈的基地址和偏移量来访问本地变量。 
8.抽空看看李维的《悟透Delphi》
有一回，我梦见自己变成了计算机时空世界里的一个对象。随着计算机世界的不断发展，我们这些对象已经不再象原始时代的对象那样仅仅为了获得生存的资源而不停的忙碌。我们的思想空前活跃，我门不但思考我们为什么要在计算机世界里生存和运行，而且还大胆的研究和探索计算机世界的未知奥秘。我们已经知道整个计算机世界都是由字节这一基本粒子构成，而字节又是由八个更细小的位粒子构成；我们还知道物质不灭定律，即任何一个对象的灭亡，只意味着对象结构的解体，并不会减少计算机世界中的任何字节或位粒子，而着这些物质又可能成为别的对象的一部分；甚至，我们还知道我们所处的世界是一个球体，因为，在越过经度$FFFFFFFF又回到了原点$00000000的位置。著名的物理学家对象牛顿早就发现各种对象之间存在一种普遍的联系，并且在对象的运动速度与对象大小的关系方面提出了著名的理论--牛顿力学。可是，后来牛顿这个对象却一直搞不懂到底是什么力量在无形地推动各种对象的运动。因此，他认为一定是创造整个计算机世界的上帝在推动各种对象的运动。后来他成了上帝最虔诚的信徒。在牛顿对象死后不久，我们的计算机世界又诞生了一个更伟大的对象。他基于先有代码的执行才有执行的结果这一基本的因果论，提出了进程运动的时空是相对的这一伟大理论。他认为，在一个运动中进程空间中看另一个运动中的进程空间，时间和空间都不是绝对的，空间会弯曲。而且，任何对象的运动速度绝对不可能超过CPU的速度，CPU速度就是我们计算机世界里的光速。这位伟大的科学对象的名字就叫爱因斯坦，他的相对论在一开始是不被对象们理解的，可是后来的科学探索都证明了这一理论的正确性。他提出的代码能量和数据物质可以相互转换的理论，也后来制造的大规模毁灭性病毒核武器中得到验证。
在梦的世界里，我快乐极了。我一会儿变一变我的属性，一会儿又动动我的方法，一会儿感受一下外来的事件。没错，我确实就是一个实实在在的对象！过了一会我突然明白，我本来就是一个对象，只是这个对象在梦中变成了现实世界的我……哈哈！
第5课
1.CWnd::CreateSolidCaret创建插入符，ShowCaret()显示插入符。GetTextMetrics(),获得当前字体的一些信息。CWnd::CreateCaret()创建图象插入符
bitmap.LoadBitmap(IDB_BITMAP1);//此处的bitmap为成员变量！！！
CreateCaret(&bitmap);
ShowCaret();
TEXTMETRIC tm;//字体结构体
dc.GetTextMetrics(&tm);//
m_ptOrigin.y+=tm.tmHeight;//获得字体高度。
2.VC中CString::LoadString(ID号)，比较方便。
3.路径层的概念：有两种方法创建路径层：
  （1）
pDC->BeginPath();
pDC->Rectangle(50,50,50+sz.cx,50+sz.cy);
pDC->EndPath();
pDC->SelectClipPath(RGN_DIFF);
   （2）
        CSize sz=pDC->GetTextExtent(str);
        CRgn rn;
        rn.CreateRectRgn(0,50,sz.cx,sz.cy);
        pDC->SelectClipRgn(&rn,RGN_DIFF);
路径层有什么作用？可以保护我们先前的文本或者图像不被后来画的覆盖。
4.在View上输入文字的步骤。
CFont font;//创建字体对象
font.CreatePointFont(300,"华文行楷",NULL);//设置
CFont *pOldFont=dc.SelectObject(&font);//将字体选择到DC中
TEXTMETRIC tm;//创建字体信息对象
dc.GetTextMetrics(&tm);//获得当前字体信息
if(0x0d==nChar)//处理回车键
{
  m_strLine.Empty();
  m_ptOrigin.y+=tm.tmHeight;
}
else if(0x08==nChar)//处理退格键
{
  COLORREF clr=dc.SetTextColor(dc.GetBkColor());
  dc.TextOut(m_ptOrigin.x,m_ptOrigin.y,m_strLine);
  m_strLine=m_strLine.Left(m_strLine.GetLength()-1);
  dc.SetTextColor(clr);
}
else
{
  m_strLine+=nChar;
}
CSize sz=dc.GetTextExtent(m_strLine);
CPoint pt;//处理光标的位置
pt.x=m_ptOrigin.x+sz.cx;
pt.y=m_ptOrigin.y;
SetCaretPos(pt);
dc.TextOut(m_ptOrigin.x,m_ptOrigin.y,m_strLine);//输出字体
dc.SelectObject(pOldFont);//将原先的字体选择回去。
5.模拟卡啦OK变色的步骤。
   （1）设置定时器
   （2）在定时器中加入如下代码
//DEL  m_nWidth+=5;//此为view的成员变量，初始值为0
//DEL 
//DEL 
//DEL  CClientDC dc(this);
//DEL  TEXTMETRIC tm;
//DEL  dc.GetTextMetrics(&tm);
//DEL  CRect rect;
//DEL  rect.left=0;
//DEL  rect.top=200;
//DEL  rect.right=m_nWidth;
//DEL  rect.bottom=rect.top+tm.tmHeight;//此长方形的长度随着定时器的触发，逐渐增大
//DEL 
//DEL  dc.SetTextColor(RGB(255,0,0));
//DEL  CString str;
//DEL  str.LoadString(IDS_WEIXIN);
//DEL  dc.DrawText(str,rect,DT_LEFT);此函数的作用是将字符串输出到长方形中，但如果字符串的长度超过长方形的长度，多余的字符将被截断
//DEL 
//DEL  rect.top=150;
//DEL  rect.bottom=rect.top+tm.tmHeight;
//DEL  dc.DrawText(str,rect,DT_RIGHT);
//DEL 
//DEL  CSize sz=dc.GetTextExtent(str);获得字符串的长度
//DEL  if(m_nWidth>sz.cx)当长方形的长度大于字符串的长度后，将其重新归0
//DEL  {
//DEL   m_nWidth=0;
//DEL   dc.SetTextColor(RGB(0,255,0));
//DEL   dc.TextOut(0,200,str);
//DEL  }
//DEL 
//DEL  CView::OnTimer(nIDEvent);
6.SetTimer也可以用回调函数来操作，但并不方便。以下是步骤
  （1） 在View的OnCreate消息响应函数中：SetTimer(1,1000,Timer2Proc);
  （2） 回调函数的实现：
void CALLBACK EXPORT Timer2Proc(
   HWND hWnd,      // handle of CWnd that called SetTimer
   UINT nMsg,      // WM_TIMER
   UINT nIDEvent,   // timer identification
   DWORD dwTime    // system time
)
{
//  MessageBox((((CMainFrame *)AfxGetMainWnd())->m_hWnd),"ddfaf","weixin",0);
;
CMainFrame *pMain=(CMainFrame *)AfxGetApp()->m_pMainWnd;//获得MainFrame的指针
CTextView *pView=(CTextView *)pMain->GetActiveView();//获得view的指针
CClientDC dc(pView);//构造DC
  dc.TextOut(333,222,"hello world");
}//我们可以看出，使用回调函数时要获得窗口或者APP的指针，给我们的操作带来麻烦。并不方便。
第6课
1.当对某菜单添加消息响应函数时，4个类的消息响应优先次序分别是：1.View;2.CDOC;3.CMainFrame.4.CWinAPP.为什么？请参阅《深入浅出》
2.消息分类：a;标准消息（以WM_开头的消息，但不包括ON_COMMAND）；b;命令消息 ON_COMMAND(IDM_PHONE1, OnPhone1)，菜单和工具栏的消息。c.通告消息：按钮，列表框发出的消息。
CCmdTarget只能接受命令消息。而从CCmdTarget派生的CWnd可以接收命令消息，也可以接受标准消息。
3.确定菜单的索引号，注意从0开始,分隔符也算数。什么叫弹出菜单（Popup Menu）?一个子菜单只能有一个缺省菜单。 //GetMenu()->GetSubMenu(0)->SetDefaultItem(5,TRUE);
str.Format("x=%d,y=%d",GetSystemMetrics(SM_CXMENUCHECK),
   GetSystemMetrics(SM_CYMENUCHECK));//获得系统的菜单的位图的大小。
/* SetMenu(NULL);//移除菜单
CMenu menu;
menu.LoadMenu(IDR_MAINFRAME);
SetMenu(&menu);
menu.Detach();*/增加菜单，此处detach(),如果是局部变量。
4.
void CMainFrame::OnUpdateEditCut(CCmdUI* pCmdUI) 
{
if(2==pCmdUI->m_nIndex)
  pCmdUI->Enable();//当此菜单显示时，设为可用。
}
5.右键弹出菜单功能的实现方法有两个：
  a.Project->Add to Project->component and controls->文件夹VC components->Popup Menu OK
  b.用TrackPopupMenu()实现。
CMenu menu;
menu.LoadMenu(IDR_MENU1);
CMenu *pPopup=menu.GetSubMenu(0);
ClientToScreen(&point);
pPopup->TrackPopupMenu(TPM_LEFTALIGN | TPM_RIGHTBUTTON, point.x, point.y,
   GetParent());
6.动态创建菜单的方法：
CMenu menu;
menu.CreatePopupMenu();
// GetMenu()->AppendMenu(MF_POPUP,(UINT)menu.m_hMenu,"WinSun");
GetMenu()->InsertMenu(2,MF_BYPOSITION | MF_POPUP,(UINT)menu.m_hMenu,"WinSun");
menu.AppendMenu(MF_STRING,IDM_HELLO,"Hello");
menu.AppendMenu(MF_STRING,112,"Weixin");
menu.AppendMenu(MF_STRING,113,"Mybole");
menu.Detach();
GetMenu()->GetSubMenu(0)->AppendMenu(MF_STRING,114,"Welcome");
GetMenu()->GetSubMenu(0)->InsertMenu(ID_FILE_OPEN,
   MF_BYCOMMAND | MF_STRING,115,"维新");
// GetMenu()->DeleteMenu(1,MF_BYPOSITION);
// GetMenu()->GetSubMenu(0)->DeleteMenu(2,MF_BYPOSITION);
7.为动态创建的菜单增加消息响应的步骤
  a.在resource.h中增加#define IDM_HELLO 123
  b.在MainFrm.h中加入afx_msg void OnHello();
  c.MainFrm.cpp中加入ON_COMMAND(IDM_HELLO,OnHello)
  d.最后加入
void CMainFrame::OnHello()
{
MessageBox("Hello!");
}
8.动态增加电话号码本步骤
  a.处理WM_Char消息。如果回车，则清空字符串，窗口重绘invalidate，将人名加入到菜单中，将字符串保存集合类CStringArray中,用的是成员函数Add方法。
  b.取出动态创建的菜单的数据的方法。
    1）创建一个弹出菜单，弹出菜单下面有4个子菜单。将子菜单的ID号连续。
    2）在resource.h中添加#define IDM_PHONE1 123....
    3）添加其消息响应函数。注意注释中的文字
BEGIN_MESSAGE_MAP(CMenu2View, CView)
//{{AFX_MSG_MAP(CMenu2View)
ON_WM_CHAR()
ON_COMMAND(ID_EDIT_COPY, OnEditCopy)//下面的4句代码原来在此处。
//}}AFX_MSG_MAP
// Standard printing commands
ON_COMMAND(IDM_PHONE1, OnPhone1)//一定要这4句代码移到此处。
ON_COMMAND(IDM_PHONE2, OnPhone2)
ON_COMMAND(IDM_PHONE3, OnPhone3)
ON_COMMAND(IDM_PHONE4, OnPhone4)
ON_COMMAND(ID_FILE_PRINT, CView::OnFilePrint)
ON_COMMAND(ID_FILE_PRINT_DIRECT, CView::OnFilePrint)
ON_COMMAND(ID_FILE_PRINT_PREVIEW, CView::OnFilePrintPreview)
END_MESSAGE_MAP()
    4）填写代码
9.如何在MainFrame中拦截OnCommand消息？答，在它增加OnCommand的消息处理函数即可。
10.错误调试方法：Missing ";" before "*"
CMenu2Doc* GetDocument();//因为CMenu2Doc是个不认识的变量，将其头文件包含进即可。
第7课
1.如果在SDI中要调用对话框
  a.先插入一个对话框资源；
  b.然后在ClassWizards中为其创建一个类。其目的是比较方便为添加按纽和消息响应函数。
  c.然后实例化它。在实例化时，必须将其头文件包含进去。
2.创建非模态对话框，注意它不能是局部变量。当 点击非模态对话框的OnOK按纽时，它并没有关闭，而是隐藏了。需要调用destroyWindow().
3.一个对象只能一个按纽。为什么？因为在Wincore.cpp的628行有代码 ASSERT(pWnd->m_hWnd == NULL);   // only do once而创建后它的m_hWnd就不为0了。此处ASSERT的用法是如果括号里面不为真，则程序崩溃。
4.如何为静态文本框增加消息响应？首先将IDC_STATIC改名。同时还需要将Notify特性复选中。
5.完成加法功能。
  a.GetDlgItem();
  b.GetDlgItemText();
  c.GetDlgItemInt();
  d.将IDC_EDIT1关联CEDIT类型变量
  e.将IDC_EDIT1关联int型变量。注意调用 UpdateData();
  f. //::SendMessage(GetDlgItem(IDC_EDIT1)->m_hWnd,WM_GETTEXT,10,(LPARAM)ch1);
//::SendMessage(m_edit1.m_hWnd,WM_GETTEXT,10,(LPARAM)ch1);
//GetDlgItem(IDC_EDIT1)->SendMessage(WM_GETTEXT,10,(LPARAM)ch1);
m_edit1.SendMessage(WM_GETTEXT,10,(LPARAM)ch1);
  g. SendDlgItemMessage(IDC_EDIT1,WM_GETTEXT,10,(LPARAM)ch1);
SendDlgItemMessage(IDC_EDIT2,WM_GETTEXT,10,(LPARAM)ch2);
6.点击按纽改变窗口尺寸
   if(GetDlgItemText(IDC_BUTTON2,str),str=="收缩<<")
{
  SetDlgItemText(IDC_BUTTON2,"扩展>>");
static CRect rectLarge;
static CRect rectSmall;

if(rectLarge.IsRectNull())
{
  CRect rectSeparator;
  GetWindowRect(&rectLarge);
  GetDlgItem(IDC_SEPARATOR)->GetWindowRect(&rectSeparator);
  rectSmall.left=rectLarge.left;
  rectSmall.top=rectLarge.top;
  rectSmall.right=rectLarge.right;
  rectSmall.bottom=rectSeparator.bottom;
}
if(str=="收缩<<")
{
  SetWindowPos(NULL,0,0,rectSmall.Width(),rectSmall.Height(),
   SWP_NOMOVE | SWP_NOZORDER);
}
else
{
  SetWindowPos(NULL,0,0,rectLarge.Width(),rectLarge.Height(),
   SWP_NOMOVE | SWP_NOZORDER);
}
7.回车时将输入焦点移动到下一个控件
SetWindowLong()改变窗口的属性。
方法1：
改变控件的回调函数，注意IDC_EDIT1的MultiLine要复选上。
WNDPROC prevProc;
LRESULT CALLBACK WinSunProc(
  HWND hwnd,      // handle to window
  UINT uMsg,      // message identifier
  WPARAM wParam,  // first message parameter
  LPARAM lParam   // second message parameter
)
{
if(uMsg==WM_CHAR && wParam==0x0d)
{
  //::SetFocus(::GetNextWindow(hwnd,GW_HWNDNEXT));
  //SetFocus(::GetWindow(hwnd,GW_HWNDNEXT));
  CString str;
  str.Format("%d",hwnd);
  AfxMessageBox(str);//, UINT nType = MB_OK, UINT nIDHelp = 0 );
//  AfxGetApp()->
  SetFocus(::GetNextDlgTabItem(::GetParent(hwnd),hwnd,FALSE));
  return 1;
}
else
{
  return prevProc(hwnd,uMsg,wParam,lParam);
}
}
BOOL CTestDlg::OnInitDialog() 
{
CDialog::OnInitDialog();

// TOD Add extra initialization here
prevProc=(WNDPROC)SetWindowLong(GetDlgItem(IDC_EDIT1)->m_hWnd,GWL_WNDPROC,
  (LONG)WinSunProc);//设置回调函数
return TRUE;  // return TRUE unless you set the focus to a control
               // EXCEPTION: OCX Property Pages should return FALSE
}
方法2：
在OnOK响应函数中加入代码
//GetDlgItem(IDC_EDIT1)->GetNextWindow()->SetFocus();
//GetFocus()->GetNextWindow()->SetFocus();
//GetFocus()->GetWindow(GW_HWNDNEXT)->SetFocus();
GetNextDlgTabItem(GetFocus())->SetFocus();
第8课  对话框
1.如何改变按纽的字体？在对话框的属性中改变字体的属性即可
2.逃跑按纽的实现
  1.从CButton派生一个类，CWeixinBtn
  2.将IDC_EDIT1关联成员变量m_btn1,类型为CWeixinBtn，注意要包含头文件。
  3.在CWeixinBtn中加一个指针成员变量CWeixinBtn *pWeixinBtn,然后将其地址初始化。
  4.在新类中增加鼠标移动的消息处理。
3.属性表单
  1.插入属性页资源。Insert->new Resource->Dialog
  2.当选择Classwizard菜单时，系统提示是否为创建新的类，我们将其从CPropertyPage派生！这样可以为
方便为其增加消息响应函数。
  3.插入新的从CPropertySheet派生的类，在类中增加3个CPropertyPage的实例。
  4.在view中增加菜单项，当点击时显示属性表单，出现中文乱码，修改CPropertyPage属性为中文，另外将
其字体设为宋体。
  5.在CPropertyPage中设置SetWizardButtons可将其属性改为上一步、完成！
  6.为IDC_RADIO1关联成员变量，需要先设置Group属性才行。另外别忘记调用UpdateData().
  7.为CPropertyPage增加虚函数，OnWizardNext,如果用户点击下一步时，不想让他进入下一步，刚返回-1
！
  8.将用户的选择输出到屏幕上，此时可以在View中增加几个成员变量，用来接收用户选择的数据。
4.memset()的用法！ memset(m_bLike,0,sizeof(m_bLike));
第9课
1.修改外观和图标可以在MainFrm中进行，而修改背景和光标只能在View中进行。为什么？因为view的显示挡
在了MainFrame的前面。
  a.在MainFrame中
      PreCreateWindow()中，在窗口创建之前，用重新注册窗口类的方法，比较麻烦。在PreCreateWindow
()中修改
      也可以用简单的方法，用全局函数
//cs.lpszClass=AfxRegisterWndClass(CS_HREDRAW | CS_VREDRAW,0,0,
// LoadIcon(NULL,IDI_WARNING));
     在窗口创建之后，在OnCreate（）中修改
//SetWindowLong(m_hWnd,GWL_STYLE,WS_OVERLAPPEDWINDOW);
//SetWindowLong(m_hWnd,GWL_STYLE,GetWindowLong(m_hWnd,GWL_STYLE) & ~WS_MAXIMIZEBOX);
// SetClassLong(m_hWnd,GCL_HICON,(LONG)LoadIcon(NULL,IDI_ERROR));
  b.在View中
    PreCreateWindow()中
//cs.lpszClass=AfxRegisterWndClass(CS_HREDRAW | CS_VREDRAW,
// LoadCursor(NULL,IDC_CROSS),(HBRUSH)GetStockObject(BLACK_BRUSH),NULL);
cs.lpszClass=AfxRegisterWndClass(CS_HREDRAW | CS_VREDRAW);
    OnCreate()中
SetClassLong(m_hWnd,GCL_HBRBACKGROUND,(LONG)GetStockObject(BLACK_BRUSH));
SetClassLong(m_hWnd,GCL_HCURSOR,(LONG)LoadCursor(NULL,IDC_HELP));
2.创建一个不断变化的图标。用定时器和SetClassLong完成
  a.准备三个图标文件，放在RES文件夹，Insert->Resource-三个图标，
  b.在CMainFrame中增加图标句柄数组，m_hIcons[3]
m_hIcons[0]=LoadIcon(AfxGetInstanceHandle(),MAKEINTRESOURCE
(IDI_ICON1));//MAKEINTRESOURCE是一个宏，它将整数转化为Win32的资源类型，简单的说它是一个类型转换
#define MAKEINTRESOURCEA(i) (LPSTR)((DWORD)((WORD)(i)))
m_hIcons[1]=LoadIcon(theApp.m_hInstance,MAKEINTRESOURCE(IDI_ICON2));//此处需要用到
theAPP对象，故要在文件中声明extern CStyleApp theApp;
m_hIcons[2]=LoadIcon(AfxGetApp()->m_hInstance,MAKEINTRESOURCE(IDI_ICON3));
然后将其初始化
  c.然后在定时器中实现
3.工具栏的编程
  a.加入分隔符的方法，向右拖动即可；
  b.删除按纽的方法，拖出即可。
4.创建一个新的工具栏的方法
  a.插入一个工具栏，画出其图形。
  b.在头文件中，定义CToolBar m_newToolBar
  c.在MainFrm.cpp的OnCreate()中调用
if (!m_newToolBar.CreateEx(this, TBSTYLE_FLAT, WS_CHILD | WS_VISIBLE | CBRS_RIGHT
  | CBRS_GRIPPER | CBRS_TOOLTIPS | CBRS_FLYBY | CBRS_SIZE_DYNAMIC) ||
  !m_newToolBar.LoadToolBar(IDR_TOOLBAR1))
{
  TRACE0("Failed to create toolbar\n");
  return -1;      // fail to create
}   
  d.点击“新的工具栏”菜单时，隐藏工具栏。两种方法
  第一种/*if(m_newToolBar.IsWindowVisible())
{
  m_newToolBar.ShowWindow(SW_HIDE);
}
else
{
  m_newToolBar.ShowWindow(SW_SHOW);
}
RecalcLayout();
DockControlBar(&m_newToolBar);*/
  第二种ShowControlBar(&m_newToolBar,!m_newToolBar.IsWindowVisible(),FALSE);
  e.将菜单增加复选标记。在OnUpdateUI中加入代码
    pCmdUI->SetCheck(m_newToolBar.IsWindowVisible());
5.状态栏编程
  a.Indicator[]数组中有状态栏的信息
  如果要增加，可以在String Table中加入一个IDS_Timer,然后将其加入到[]中。
  b.在时间栏显示时间，代码略，比较简单
6.进度栏
  a.增加成员变量，CProgressCtrl m_progress
  b.OnCreate中 m_progress.Create(WS_CHILD | WS_VISIBLE,// | PBS_VERTICAL,
  rect,&m_wndStatusBar,123);
m_progress.SetPos(50);*/
  c.将其创建到状态栏的方法！如果在OnCreate()中创建，则不成立，因为获取矩形大小时失败。
    解决办法，用自定义消息：
    在MainFrm.h中#define UM_PROGRESS  WM_USER+1
afx_msg void OnProgress();
    在MainFrm.cpp中
ON_MESSAGE(UM_PROGRESS,OnProgress)
然后实现这个函数
void CMainFrame::OnProgress()
{
CRect rect;
m_wndStatusBar.GetItemRect(2,&rect);
m_progress.Create(WS_CHILD | WS_VISIBLE | PBS_SMOOTH,
  rect,&m_wndStatusBar,123);
m_progress.SetPos(50);
}
     最后在OnCreate中调用 PostMessage(UM_PROGRESS);//不能用SendMessage()
   d.解决重绘时进度栏改变的问题。在OnPain()中重写代码
CRect rect;
m_wndStatusBar.GetItemRect(2,&rect);
m_progress.Create(WS_CHILD | WS_VISIBLE | PBS_SMOOTH,
  rect,&m_wndStatusBar,123);
m_progress.SetPos(50);
然后在定时器消息处理函数中加入
m_progress.StepIt();
   e.显示鼠标位置。在View中增加OnMouseMove()处理函数
CString str;
str.Format("x=%d,y=%d",point.x,point.y);
//((CMainFrame*)GetParent())->m_wndStatusBar.SetWindowText(str);
//((CMainFrame*)GetParent())->SetMessageText(str);
//((CMainFrame*)GetParent())->GetMessageBar()->SetWindowText(str);
GetParent()->GetDescendantWindow(AFX_IDW_STATUS_BAR)->SetWindowText(str);
7.加入启动画面
  Project-Component and ->Visual C++ Components->SplashScreen->插入
第10课
1.画图：
   a.创建四个菜单，为其添加消息响应；
   b.在View中添加m_DrawType,保存绘画类型；
   c.增加成员变量，m_PtOrigin，当按下鼠标左键时，保存此点；
   d.在OnLButtonUp中画点，线，矩形，椭圆，别忘记设置成透明画刷
2.为其添加一个设置对话框（线型和线宽）
   a.创建对话框，为其创建一个新类关联它；
   b.为其中的线宽关联成员变量；
   c.在View中增加一个菜单，响应新的对话框；
   d.添加线型选项设置，将其Group属性选中，并为单选按纽关联成员变量。在view中增加一个线型变量m_nLineStyle
3.添加一个颜色对话框
   a.实例化一个CColorDialog
   b.调用DoModal方法
4.添加字体对话框，将选择的字体在View中显示出来。
   a.实例化一个对象；
   b.为View添加一个字体成员变量，得到用户选择的字体。
   c.调用Invadate()发出重绘消息；
   d.再次注意一个对象只能创建一次，故要再次创建，必须将原告的删除！
5.为设置对话框增加示例功能。
   a.当控件内容改变时，发出En_change消息。而Radio按纽则为Clicked。需先UpdateData()。另外还需要ScreenToClient(&rect)
6.改变对话框的背景色和控件颜色。
  每个控件被绘制时都发出WM_CTlColor消息，
7.如何改变OK按纽的字体和背景？
  OK按纽
  a.创建一个新类，CTestBtn,基类为CButton
  b.在类中增加虚函数，DrawItem，添加代码。
  c.将OK按纽关联成员变量。类型为CTestBtn，注意将OK按纽的OwnerDraw特性选中。
  Cancel按纽
  用新类来改变。
  a.加入新文件。
  b.为Cancel关联一个成员变量，类型为CSXBtn;
  c.调用CSXBtn的方法。
  Cancel2按纽
  a.方法同上。
8.在窗口中贴图,4个步骤
  1、创建位图
CBitmap bitmap;
bitmap.LoadBitmap(IDB_BITMAP1);
2、创建兼容DC
CDC dcCompatible;
dcCompatible.CreateCompatibleDC(pDC);
3、将位图选到兼容DC中
dcCompatible.SelectObject(&bitmap);
4、将兼容DC中的位图贴到当前DC中。在WM_EraseBkgnd()中调用，但不能再调用基类的擦除背景函数。也可以在OnDraw函数中完成，但效率低，图像会闪烁，因为它先擦除背景，慢。
pDC->BitBlt(rect.left,rect.top,rect.Width(),
rect.Height(),&dcCompatible,0,0,SRCCOPY);
第12课 文件操作
1.常量指针与指针常量的区分
  char ch[5]="lisi";
  const char *pStr=ch;//const在*之前，表明指针指向的内容为常量，即为常量指针
  char * const pStr=ch;//const在*之后，表明指针的地址不能改变，即为指针常量
  明白？
2.对文件读写的三种方法
  1.C中
    FILE *pFile=fopen("1.txt","w");
fwrite("http://www.sunxin.org",1,strlen("http://www.sunxin.org"),pFile);
//fseek(pFile,0,SEEK_SET);
//fwrite("ftp:",1,strlen("ftp:"),pFile);
//fwrite("http://www.sunxin.org",1,strlen("http://www.sunxin.org"),pFile);
fclose(pFile);*/
//fflush(pFile);
  2.C++中
/* ofstream ofs("4.txt");
ofs.write("http://www.sunxin.org",strlen("http://www.sunxin.org"));
ofs.close();*/
         要包括头文件 "fstream.h"
  3.MFC中 用CFile类，哈哈！简单好用
CFileDialog fileDlg(FALSE);
fileDlg.m_ofn.lpstrTitle="我的文件保存对话框";
fileDlg.m_ofn.lpstrFilter="Text Files(*.txt)\0*.txt\0All Files(*.*)\0*.*\0\0";
fileDlg.m_ofn.lpstrDefExt="txt";
if(IDOK==fileDlg.DoModal())
{
  CFile file(fileDlg.GetFileName(),CFile::modeCreate | CFile::modeWrite);
  file.Write("http://www.sunxin.org",strlen("http://www.sunxin.org"));
  file.Close();
}
  4.利用win32 API函数 CreateFile(),及WriteFile()
4.注册表读写
  1.对win.ini的读写
//::WriteProfileString("http://www.sunxin.org","admin","zhangsan");
/* CString str;
::GetProfileString("http://www.sunxin.org","admin","lisi",
  str.GetBuffer(100),100);
AfxMessageBox(str);*/
  2.注册表的读写
HKEY hKey;
DWORD dwAge=30;
RegCreateKey(HKEY_LOCAL_MACHINE,"Software\\http://www.sunxin.org\\admin",&hKey);
RegSetValue(hKey,NULL,REG_SZ,"zhangsan",strlen("zhangsan"));
RegSetValueEx(hKey,"age",0,REG_DWORD,(CONST BYTE*)&dwAge,4);
RegCloseKey(hKey);以上是写入
代码比较简单，不再详细介绍。本笔记也不是为介绍函数而存在的。嘿嘿
第13课 文档与串行化
1.CArchive在菜单打开保存时的代码
CFile file("1.txt",CFile::modeCreate | CFile::modeWrite);
CArchive ar(&file,CArchive::store);
int i=4;
char ch='a';
float f=1.3f;
CString str("http://www.sunxin.org");
ar<<i<<ch<<f<<str;以上是保存，打开略
2.文档-视类结构简介
   OnNewDocument在程序启动时被调用，此时可设置文档标题，也可以在String Table的IDR_MAINFRAME的第二个"\"后改变文档的标题。须了解的7个字符串的用途，见PPT。
   在WinAPP的InitInstance（）中完成DOC,View,MainFrame的归一。
   当点击系统的打开和新建菜单时，有一系列的步骤，孙鑫老师给我们跟踪了代码的调用过程，此段跟踪我们略过。但我们要牢记住：CWinAPP负责管理文档管理器，文档管理器有一个指针链表，且来保存文档模板的指针，文档模板指针管理三个类DOC，VIEW，MAINFRAME，使其为某文件对象服务。
3.利用CArchive来保存一个类的对象，此类必须支持串行化，需要5个步骤。
  a.让类从CObject派生；
  b.覆盖Serialize()函数，在其中完成保存和读取功能；
  c.在.h中加入 DECLARE_SERIAL(CGraph)；
  d.在。cpp中加入IMPLEMENT_SERIAL(CGraph, CObject, 1 )；
  e.定义一个不带参数的构造函数。
保存绘画数据到文件的简单过程
  a.在CGraph中增加一个画图的成员函数，其实不增加也行。可以在View中完成相应功能。
  b.增加四个画图菜单，菜单可以从11课的代码中拷贝。
  c.在View中增加LButtonDown和UP的响应，在UP中画图，在DOWN中保存点
  d.利用CObArray集合类来保存绘画数据
  e.在CGraphicDOC::Serialize()中保存和读取数据
  f.然后在OnDraw中重绘。
4.新建和打开文档时，要注意销毁原来的数据。在DOC的DeleteContents虚函数中是好时机。代码如下
int nCount;
nCount=m_obArray.GetSize();
/*for(int i=0;i<nCount;i++)
{
  delete m_obArray.GetAt(i);//释放指针指向的内存空间
  //m_obArray.RemoveAt(i);//移除链表中的元素。嘿嘿，别搞错了。但在此处不能这样用，会导致非法操作。要用下面的方法沙
}
m_obArray.RemoveAll();*/
while(nCount--)
{
  delete m_obArray.GetAt(nCount);
  m_obArray.RemoveAt(nCount);
}
第14课 网络编程
1.TCP流式套接字的编程步骤
在使用之前须链接库函数：工程->设置->Link->输入ws2_32.lib，OK！
服务器端程序：
1、加载套接字库
2、创建套接字（socket）。 
3、将套接字绑定到一个本地地址和端口上（bind）。
4、将套接字设为监听模式，准备接收客户请求（listen）。
5、等待客户请求到来；当请求到来后，接受连接请求，返回一个新的对应于此次连接的套接字（accept）。
6、用返回的套接字和客户端进行通信（send/recv）。
7、返回，等待另一客户请求。
8、关闭套接字。
客户端程序：
1、加载套接字库
2、创建套接字（socket）。 
3、向服务器发出连接请求（connect）。
4、和服务器端进行通信（send/recv）。
5、关闭套接字。
服务器端代码如下：
#include <Winsock2.h>//加裁头文件
#include <stdio.h>//加载标准输入输出头文件
void main()
{
WORD wVersionRequested;//版本号
WSADATA wsaData;
int err;

wVersionRequested = MAKEWORD( 1, 1 );//1.1版本的套接字

err = WSAStartup( wVersionRequested, &wsaData );
if ( err != 0 ) {
  return;
}//加载套接字库，加裁失败则返回
if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
  WSACleanup( );
  return; 
}//如果不是1.1的则退出
SOCKET sockSrv=socket(AF_INET,SOCK_STREAM,0);//创建套接字（socket）。
SOCKADDR_IN addrSrv;
addrSrv.sin_addr.S_un.S_addr=htonl(INADDR_ANY);//转换Unsigned short为网络字节序的格式
addrSrv.sin_family=AF_INET;
addrSrv.sin_port=htons(6000);
bind(sockSrv,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));
//将套接字绑定到一个本地地址和端口上（bind）
listen(sockSrv,5);//将套接字设为监听模式，准备接收客户请求（listen）。
SOCKADDR_IN addrClient;//定义地址族
int len=sizeof(SOCKADDR);//初始化这个参数，这个参数必须被初始化
while(1)
{
  SOCKET sockConn=accept(sockSrv,(SOCKADDR*)&addrClient,&len);accept的第三个参数一定要有初始值。
//等待客户请求到来；当请求到来后，接受连接请求，返回一个新的对应于此次连接的套接字（accept）。
//此时程序在此发生阻塞
  char sendBuf[100];
  sprintf(sendBuf,"Welcome %s to http://www.sunxin.org",
   inet_ntoa(addrClient.sin_addr));
//用返回的套接字和客户端进行通信（send/recv）。
  send(sockConn,sendBuf,strlen(sendBuf)+1,0);
  char recvBuf[100];
  recv(sockConn,recvBuf,100,0);
  printf("%s\n",recvBuf);
  closesocket(sockConn);//关闭套接字。等待另一个用户请求
}
}
客户端代码如下：
#include <Winsock2.h>
#include <stdio.h>
void main()
{
WORD wVersionRequested;
WSADATA wsaData;
int err;

wVersionRequested = MAKEWORD( 1, 1 );

err = WSAStartup( wVersionRequested, &wsaData );加载套接字库
if ( err != 0 ) {
  return;
}
if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
  WSACleanup( );
  return; 
}
SOCKET sockClient=socket(AF_INET,SOCK_STREAM,0);创建套接字（socket）。
SOCKADDR_IN addrSrv;
addrSrv.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
addrSrv.sin_family=AF_INET;
addrSrv.sin_port=htons(6000);
connect(sockClient,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));向服务器发出连接请求（connect）。
char recvBuf[100];和服务器端进行通信（send/recv）。
recv(sockClient,recvBuf,100,0);
printf("%s\n",recvBuf);
send(sockClient,"This is lisi",strlen("This is lisi")+1,0);
closesocket(sockClient);关闭套接字。
WSACleanup();//必须调用这个函数清除参数
}
2.UDP型套接字。
服务器端（接收端）程序：
1、创建套接字（socket）。 
2、将套接字绑定到一个本地地址和端口上（bind）。
3、等待接收数据（recvfrom）。
4、关闭套接字。
客户端（发送端）程序：
1、创建套接字（socket）。 
2、向服务器发送数据（sendto）。
3、关闭套接字。
服务器端代码：
#include <Winsock2.h>
#include <stdio.h>
void main()
{
WORD wVersionRequested;
WSADATA wsaData;
int err;

wVersionRequested = MAKEWORD( 1, 1 );

err = WSAStartup( wVersionRequested, &wsaData );
if ( err != 0 ) {
  return;
}
if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
  WSACleanup( );
  return; 
}
SOCKET sockSrv=socket(AF_INET,SOCK_DGRAM,0);
SOCKADDR_IN addrSrv;
addrSrv.sin_addr.S_un.S_addr=htonl(INADDR_ANY);
addrSrv.sin_family=AF_INET;
addrSrv.sin_port=htons(6000);
bind(sockSrv,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));
SOCKADDR_IN addrClient;
int len=sizeof(SOCKADDR);
char recvBuf[100];
recvfrom(sockSrv,recvBuf,100,0,(SOCKADDR*)&addrClient,&len);
printf("%s\n",recvBuf);
closesocket(sockSrv);
WSACleanup();
}
客户端代码：
#include <Winsock2.h>
#include <stdio.h>
void main()
{
WORD wVersionRequested;
WSADATA wsaData;
int err;

wVersionRequested = MAKEWORD( 1, 1 );

err = WSAStartup( wVersionRequested, &wsaData );
if ( err != 0 ) {
  return;
}
if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
  WSACleanup( );
  return; 
}
SOCKET sockClient=socket(AF_INET,SOCK_DGRAM,0);
SOCKADDR_IN addrSrv;
addrSrv.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
addrSrv.sin_family=AF_INET;
addrSrv.sin_port=htons(6000);
sendto(sockClient,"Hello",strlen("Hello")+1,0,
  (SOCKADDR*)&addrSrv,sizeof(SOCKADDR));
closesocket(sockClient);
WSACleanup();
}
UDP的不再加注释了。因为它比TCP的简单多了。
3.基于字符界面的聊天程序，用的是UDP式套接字。代码略。
4.如何添加新的工程？
首先选择中Build工具栏，然后在工程管理器上点击右键，选择增加新的工程即可。
第15课多线程与网络编程
1.多线程介绍，略
2.一个简单的多线程程序
MSND中参数[in]和[out]的含义要注意
#include <windows.h>
#include <iostream.h>
DWORD WINAPI Fun1Proc(
  LPVOID lpParameter   // thread data
);
DWORD WINAPI Fun2Proc(
  LPVOID lpParameter   // thread data
);
int index=0;
int tickets=100;
HANDLE hMutex;互斥对象的句柄
void main()
{
HANDLE hThread1;
HANDLE hThread2;
hThread1=CreateThread(NULL,0,Fun1Proc,NULL,0,NULL);创建线程1
hThread2=CreateThread(NULL,0,Fun2Proc,NULL,0,NULL);创建线程2
CloseHandle(hThread1);关闭线程的句柄，为什么要关闭？它将线程的使用计数减1
CloseHandle(hThread2);这样当线程结束时，线程内核对象被释放，否则只有当进程结束，才释放线程的内核对象
/*while(index++<1000)
  cout<<"main thread is running"<<endl;*/
//hMutex=CreateMutex(NULL,TRUE,NULL);将第二个参数设为true后，互斥对象的计数加1
hMutex=CreateMutex(NULL,TRUE,"tickets");此段代码可以让系统只一份实例在运行！
if(hMutex)
{
  if(ERROR_ALREADY_EXISTS==GetLastError())
  {
   cout<<"only instance can run!"<<endl;
   return;
  }
}
WaitForSingleObject(hMutex,INFINITE);此代码也将互斥对象的计数加1
ReleaseMutex(hMutex);所以要释放两次互斥对象
ReleaseMutex(hMutex);
Sleep(4000);睡眠4000毫秒
// Sleep(10);
}
DWORD WINAPI Fun1Proc(
  LPVOID lpParameter   // thread data
)
{
/*while(index++<1000)
  cout<<"thread1 is running"<<endl;*/

/*while(TRUE)
{
  //ReleaseMutex(hMutex);
  WaitForSingleObject(hMutex,INFINITE);等待互斥对象的到来，到来后将互斥对象的计数加1
  if(tickets>0)
  {
   Sleep(1);
   cout<<"thread1 sell ticket : "<<tickets--<<endl;
  }
  else
   break;
  ReleaseMutex(hMutex);释放互斥对象，将其计数减1，这样可以保证，这两句话之间 的代码！的执行连续性！
}*/
WaitForSingleObject(hMutex,INFINITE);
cout<<"thread1 is running"<<endl;
return 0;
}
DWORD WINAPI Fun2Proc(
  LPVOID lpParameter   // thread data
)
{

/*while(TRUE)
{
  //ReleaseMutex(hMutex);
  WaitForSingleObject(hMutex,INFINITE);
  if(tickets>0)
  {
   Sleep(1);
   cout<<"thread2 sell ticket : "<<tickets--<<endl;
  }
  else
   break;
  ReleaseMutex(hMutex);
}*/
WaitForSingleObject(hMutex,INFINITE);
cout<<"thread2 is running"<<endl;
return 0;
}
3.多线程聊天程序
  1.加载套接字库在InitInstance()中，调用AfxSocketInit(),此时可以不加载库文件，但要加入Afxsock.h"头文件
  2.在CChatDlg中创建成员变量m_socket,然后增加一个成员函数，IniSocket(),在其中完成m_socket的初始化和绑定。在OnInitDialog中调用InitSocket完成初始化工作。
  3.定义一个结构体，包含两个参数，sock和hwnd，在OnInitDialog()中初始化这个结构体的对象。
  4.创建一个线程，CreateThread(),须将线程函数RecvProc定义为静态的或者全局函数。
    ::PostMessage()完成将收到的数据发送给对话框。用自定义的消息，自定义的消息如何写？以前说过，参考下面的代码。注意要将EDitBox的MultiLine属性选上。  
    在ChatDlg.h中#define WM_RECVDATA  WM_USER+1
afx_msg void OnRecvData(WPARAM wParam,LPARAM lParam);
    在ChatDlg.cpp中
ON_MESSAGE(WM_RECVDATA,OnRecvData)
然后实现这个函数
void CChatDlg::OnRecvData(WPARAM wParam,LPARAM lParam)
{
CString str=(char*)lParam;
CString strTemp;
GetDlgItemText(IDC_EDIT_RECV,strTemp);
str+="\r\n";
str+=strTemp;
SetDlgItemText(IDC_EDIT_RECV,str);
}
     最后在DWORD WINAPI CChatDlg::RecvProc(LPVOID lpParameter)
中调用 ::PostMessage(hwnd,WM_RECVDATA,0,(LPARAM)tempBuf);
//不能用SendMessage()
    4.对发送按纽的响应代码：
void CChatDlg::OnBtnSend() 
{
// TOD Add your control notification handler code here
DWORD dwIP;
((CIPAddressCtrl*)GetDlgItem(IDC_IPADDRESS1))->GetAddress(dwIP);
SOCKADDR_IN addrTo;
addrTo.sin_family=AF_INET;
addrTo.sin_port=htons(6000);
addrTo.sin_addr.S_un.S_addr=htonl(dwIP);
CString strSend;
GetDlgItemText(IDC_EDIT_SEND,strSend);
sendto(m_socket,strSend,strSend.GetLength()+1,0,
  (SOCKADDR*)&addrTo,sizeof(SOCKADDR));
SetDlgItemText(IDC_EDIT_SEND,"");
}
第16课 
1.事件对象：来实现线程的同步。与互斥对象一样均属于内核对象。
            当人工重置有信号时，所有线程均得到信号，所以不能设为人工重置。代码就不贴了。
            通过创建匿名的事件对象，也可以让一个程序只能运行一个实例。  
2.关键代码段实现线程的同步：类似公用电话亭，只有当电话亭里面没人了，其它人才可以再进去打电话。用了4个函数，这种方法比较简单！但缺点是如果使用了多少关键代码码，容易赞成线程的死锁
3.线程死锁，用关键代码示例，用了两个临界区对象，实战中要注意避免这种错误！
4.使用异步套接字编写网络聊天室
  1)加载套接字库，进行版本协商，包含头文件，链接库文件，这次请示的是2.2版本！
  2）在类CChatDlg中增加一个成员变量m_socket,在析构函数中释放这个变量
  3）利用WSASocket()创建套接字（数据报类型的UDP型的）
  4）然后调用WSAAsyncSelect(m_socket,m_hWnd,UM_SOCK,FD_READ)为网络事件定义消息！此时如果发生FD_READ消息，系统会发送UM_SOCK消息给应用程序！程序并不会阻塞在这儿了！
  以上是在BOOL CChatDlg::OnInitDialog()完成
  5）然后完成消息响应！
  头文件中：#define UM_SOCK  WM_USER+1
afx_msg void OnSock(WPARAM,LPARAM);
   源文件中：
    ON_MESSAGE(UM_SOCK,OnSock)
    实现消息响应函数：void CChatDlg::OnSock(WPARAM wParam,LPARAM lParam)
{
switch(LOWORD(lParam))
{
case FD_READ:
  WSABUF wsabuf;
  wsabuf.buf=new char[200];
  wsabuf.len=200;
  DWORD dwRead;
  DWORD dwFlag=0;
  SOCKADDR_IN addrFrom;
  int len=sizeof(SOCKADDR);
  CString str;
  CString strTemp;
  HOSTENT *pHost;
  if(SOCKET_ERROR==WSARecvFrom(m_socket,&wsabuf,1,&dwRead,&dwFlag,
      (SOCKADDR*)&addrFrom,&len,NULL,NULL))
  {
   MessageBox("接收数据失败！");
   return;
  }
  pHost=gethostbyaddr((char*)&addrFrom.sin_addr.S_un.S_addr,4,AF_INET);
  //str.Format("%s说 :%s",inet_ntoa(addrFrom.sin_addr),wsabuf.buf);
  str.Format("%s说 :%s",pHost->h_name,wsabuf.buf);
  str+="\r\n";
  GetDlgItemText(IDC_EDIT_RECV,strTemp);
  str+=strTemp;
  SetDlgItemText(IDC_EDIT_RECV,str);
  break;
}
}
OK！
      6）完成数据发送的功能！
      void CChatDlg::OnBtnSend() 
{
// TOD Add your control notification handler code here
DWORD dwIP;
CString strSend;
WSABUF wsabuf;
DWORD dwSend;
int len;
CString strHostName;
SOCKADDR_IN addrTo;
HOSTENT* pHost;
if(GetDlgItemText(IDC_EDIT_HOSTNAME,strHostName),strHostName=="")
{
  ((CIPAddressCtrl*)GetDlgItem(IDC_IPADDRESS1))->GetAddress(dwIP);
  addrTo.sin_addr.S_un.S_addr=htonl(dwIP);
}
else
{
  pHost=gethostbyname(strHostName);
  addrTo.sin_addr.S_un.S_addr=*((DWORD*)pHost->h_addr_list[0]);
}

addrTo.sin_family=AF_INET;
addrTo.sin_port=htons(6000);
GetDlgItemText(IDC_EDIT_SEND,strSend);
len=strSend.GetLength();
wsabuf.buf=strSend.GetBuffer(len);
wsabuf.len=len+1;
SetDlgItemText(IDC_EDIT_SEND,"");
if(SOCKET_ERROR==WSASendTo(m_socket,&wsabuf,1,&dwSend,0,
   (SOCKADDR*)&addrTo,sizeof(SOCKADDR),NULL,NULL))
{
  MessageBox("发送数据失败！");
  return;
}

}
      7）完成将主机名转换为IP地址的功能，以前将IP地址转换为主机名的功能
嘿嘿，单线程的聊天室创建完毕！性能并且非常出色！
第17课 进程间通信
有四种方法
1.剪贴板
  a.创建个ClipBoard的对话框应用程序，加两EditBox和两个Button发送接收。
  b.具体代码：
    发送端代码：
if(OpenClipboard())
{
  CString str;
  HANDLE hClip;
  char *pBuf;
  EmptyClipboard();
  GetDlgItemText(IDC_EDIT_SEND,str);
  hClip=GlobalAlloc(GMEM_MOVEABLE,str.GetLength()+1);
  pBuf=(char*)GlobalLock(hClip);将句柄转换为指针！
  strcpy(pBuf,str);
  GlobalUnlock(hClip);
  SetClipboardData(CF_TEXT,hClip);
  CloseClipboard();
}
     接收端代码：
if(OpenClipboard())
{
  if(IsClipboardFormatAvailable(CF_TEXT))
  {
   HANDLE hClip;
   char *pBuf;
   hClip=GetClipboardData(CF_TEXT);
   pBuf=(char*)GlobalLock(hClip);
   GlobalUnlock(hClip);
   SetDlgItemText(IDC_EDIT_RECV,pBuf);
   CloseClipboard();
  }
}
2.匿名管道：只能在父子进程之间进行通信
  a.先建一个Parent的单文档应用程序，增加“创建管道”“读取数据”“写入数据”三个菜单
  b.增加成员变量HANDLE类型的hRead,hWrite，初始化变量，并在析构函数中释放句柄
  c.响应菜单代码：
void CParentView::OnPipeCreate() 菜单“创建管道”代码
{
// TOD Add your command handler code here
SECURITY_ATTRIBUTES sa;
sa.bInheritHandle=TRUE;
sa.lpSecurityDescriptor=NULL;
sa.nLength=sizeof(SECURITY_ATTRIBUTES);
if(!CreatePipe(&hRead,&hWrite,&sa,0))
{
  MessageBox("创建匿名管道失败！");
  return;
}
STARTUPINFO sui;
PROCESS_INFORMATION pi;
ZeroMemory(&sui,sizeof(STARTUPINFO));将数据清0！
sui.cb=sizeof(STARTUPINFO);
sui.dwFlags=STARTF_USESTDHANDLES;
sui.hStdInput=hRead;
sui.hStdOutput=hWrite;
sui.hStdError=GetStdHandle(STD_ERROR_HANDLE);

if(!CreateProcess("..\\Child\\Debug\\Child.exe",NULL,NULL,NULL,
   TRUE,0,NULL,NULL,&sui,&pi))创建子进程
{
  CloseHandle(hRead);
  CloseHandle(hWrite);关闭句柄，将内核对象的使用计数减少1，这样当操作系统发现内核对象的使用计数为0时，将清除内核对象。
  hRead=NULL;
  hWrite=NULL;
  MessageBox("创建子进程失败！");
  return;
}
else
{
  CloseHandle(pi.hProcess);
  CloseHandle(pi.hThread);
}
}
void CParentView::OnPipeRead() 菜单“读取数据”代码
{
// TOD Add your command handler code here
char buf[100];
DWORD dwRead;
if(!ReadFile(hRead,buf,100,&dwRead,NULL))
{
  MessageBox("读取数据失败！");
  return;
}
MessageBox(buf);
}
void CParentView::OnPipeWrite() 菜单“写入数据”代码
{
// TOD Add your command handler code here
char buf[]="http://www.sunxin.org";
DWORD dwWrite;
if(!WriteFile(hWrite,buf,strlen(buf)+1,&dwWrite,NULL))
{
  MessageBox("写入数据失败！");
  return;
}
}
     d.再建一个Child的单文档，在View中增加两个成员hRead和hWrite.在OnInitialUpdate()中得到句柄的值。
void CChildView::OnInitialUpdate() 
{
CView::OnInitialUpdate();

// TOD Add your specialized code here and/or call the base class
hRead=GetStdHandle(STD_INPUT_HANDLE);注意这句代码！
hWrite=GetStdHandle(STD_OUTPUT_HANDLE);
}
     e.加菜单“读取数据”“写入数据”其代码如下：
void CChildView::OnPipeRead() 
{
// TOD Add your command handler code here
char buf[100];
DWORD dwRead;
if(!ReadFile(hRead,buf,100,&dwRead,NULL))
{
  MessageBox("读取数据失败！");
  return;
}
MessageBox(buf);
}
void CChildView::OnPipeWrite() 
{
// TOD Add your command handler code here
char buf[]="匿名管道测试程序";
DWORD dwWrite;
if(!WriteFile(hWrite,buf,strlen(buf)+1,&dwWrite,NULL))
{
  MessageBox("写入数据失败！");
  return;
}
}
3.命名管道：还可以跨网络通信，服务器只能在win2000和NT下运行！而客户端可以在95下运行。关键函数CreateNamedPipe
  a.先建一个NamedPipeSRV单文档应用程序，加菜单“创建管道”“读取数据”“写入数据”
  b.在View中增加Handle变量hPipe，注意在析构函数中释放它！
  c.响应菜单，创建命名管道
void CNamedPipeSrvView::OnPipeCreate() 
{
// TOD Add your command handler code here
hPipe=CreateNamedPipe("\\\\.\\pipe\\MyPipe",
  PIPE_ACCESS_DUPLEX | FILE_FLAG_OVERLAPPED,
  0,1,1024,1024,0,NULL);
if(INVALID_HANDLE_VALUE==hPipe)
{
  MessageBox("创建命名管道失败！");
  hPipe=NULL;
  return;
}
HANDLE hEvent;
hEvent=CreateEvent(NULL,TRUE,FALSE,NULL);
if(!hEvent)
{
  MessageBox("创建事件对象失败！");
  CloseHandle(hPipe);
  hPipe=NULL;
  return;
}
OVERLAPPED ovlap;
ZeroMemory(&ovlap,sizeof(OVERLAPPED));
ovlap.hEvent=hEvent;
if(!ConnectNamedPipe(hPipe,&ovlap))
{
  if(ERROR_IO_PENDING!=GetLastError())
  {
   MessageBox("等待客户端连接失败！");
   CloseHandle(hPipe);
   CloseHandle(hEvent);
   hPipe=NULL;
   return;
  }
}
if(WAIT_FAILED==WaitForSingleObject(hEvent,INFINITE))
{
  MessageBox("等待对象失败！");
  CloseHandle(hPipe);
  CloseHandle(hEvent);
  hPipe=NULL;
  return;
}
CloseHandle(hEvent);
}
void CNamedPipeSrvView::OnPipeRead() 
{
// TOD Add your command handler code here
char buf[100];
DWORD dwRead;
if(!ReadFile(hPipe,buf,100,&dwRead,NULL))
{
  MessageBox("读取数据失败！");
  return;
}
MessageBox(buf);
}
void CNamedPipeSrvView::OnPipeWrite() 
{
// TOD Add your command handler code here
char buf[]="http://www.sunxin.org";
DWORD dwWrite;
if(!WriteFile(hPipe,buf,strlen(buf)+1,&dwWrite,NULL))
{
  MessageBox("写入数据失败！");
  return;
}
}
      d.再建一个NamedPipeCLT单文档工程，加菜单“连接管道”“读取数据”“写入数据”，当然别忘记成员变量hPipe的定义和初始化
      e.响应菜单代码
void CNamedPipeCltView::OnPipeConnect() 连接管道
{
// TOD Add your command handler code here
if(!WaitNamedPipe("\\\\.\\pipe\\MyPipe",NMPWAIT_WAIT_FOREVER))
{
  MessageBox("当前没有可利用的命名管道实例！");
  return;
}
hPipe=CreateFile("\\\\.\\pipe\\MyPipe",GENERIC_READ | GENERIC_WRITE,
  0,NULL,OPEN_EXISTING,FILE_ATTRIBUTE_NORMAL,NULL);
if(INVALID_HANDLE_VALUE==hPipe)
{
  MessageBox("打开命名管道失败！");
  hPipe=NULL;
  return;
}
}
void CNamedPipeCltView::OnPipeRead() 读取数据
{
// TOD Add your command handler code here
char buf[100];
DWORD dwRead;
if(!ReadFile(hPipe,buf,100,&dwRead,NULL))
{
  MessageBox("读取数据失败！");
  return;
}
MessageBox(buf);
}
void CNamedPipeCltView::OnPipeWrite() 写入数据
{
// TOD Add your command handler code here
char buf[]="命名管道测试程序";
DWORD dwWrite;
if(!WriteFile(hPipe,buf,strlen(buf)+1,&dwWrite,NULL))
{
  MessageBox("写入数据失败！");
  return;
}
}
4.邮槽，使用时应将消息长度限制在424字节以下，关键函数CreateMailSlot()
  a.先建一个MailSlotSRV工程，加菜单“接收数据”
  b.消息响应代码：
void CMailslotSrvView::OnMailslotRecv() 菜单“接收数据”的代码
{
// TOD Add your command handler code here
HANDLE hMailslot;
hMailslot=CreateMailslot("\\\\.\\mailslot\\MyMailslot",0,
  MAILSLOT_WAIT_FOREVER,NULL);
if(INVALID_HANDLE_VALUE==hMailslot)
{
  MessageBox("创建油槽失败！");
  return;
}
char buf[100];
DWORD dwRead;
if(!ReadFile(hMailslot,buf,100,&dwRead,NULL))
{
  MessageBox("读取数据失败！");
  CloseHandle(hMailslot);
  return;
}
MessageBox(buf);
CloseHandle(hMailslot);
}
    c.加工程MailSlotCLT,加菜单“发送数据”
    d.加消息响应，添加代码，客户端也比较简单。
void CMailslotCltView::OnMailslotSend() 菜单“发送数据”的代码
{
// TOD Add your command handler code here
HANDLE hMailslot;
hMailslot=CreateFile("\\\\.\\mailslot\\MyMailslot",GENERIC_WRITE,
  FILE_SHARE_READ,NULL,OPEN_EXISTING,FILE_ATTRIBUTE_NORMAL,NULL);
if(INVALID_HANDLE_VALUE==hMailslot)
{
  MessageBox("打开油槽失败！");
  return;
}
char buf[]="http://www.sunxin.org";
DWORD dwWrite;
if(!WriteFile(hMailslot,buf,strlen(buf)+1,&dwWrite,NULL))
{
  MessageBox("写入数据失败！");
  CloseHandle(hMailslot);
  return;
}
CloseHandle(hMailslot);
}
5.以上4种方法各有优缺点：剪贴板比较简单。邮槽是基于广播的，可以一对多发送。但只能一个发送，一个接收，要想同时发送接收，须写两次代码。
命名管道和邮槽可以进行网络通信。
第18课 ActiveX编程（下面X均为ActiveX简称）
1.在VB中调用X控件，添加方法 project->Add components。另外可以用Object Browser来查看控件
2.在VC中创建X控件
  1.新建一个X工程名为Clock，注意一个文件中可以包含多个控件。
  2.保持缺省设置，完成。注意它生成的三个类，以及相关的接口。
  3.运行它。选择TSTCON32.exe作为容器。
  4.选择Insert Control,此时我们可以看到，它画了一个椭圆。也可以在VB中测试。
  5.删除注册信息。用regsvr32 /u +文件名。也可以在菜单选择反注册命令。
  6.重写代码。在CClockCtrl::OnDraw()中画了一个椭圆，此时我们在其中得到系统时间，并显示它。为此我们在OnCreate()设置了一个定时器，每隔一定时间发出一个Invalidate()消息，使窗口重绘。
  7.如何改变控件的背景色和前景色？ClassWizard->AutoMation->Add Property->BackColor,还需要在OnDraw()中加上相应的代码
   CBrush brush(TranslateColor(GetBackColor()));
pdc->FillRect(rcBounds, &brush);
pdc->SetBkMode(TRANSPARENT);
pdc->SetTextColor(TranslateColor(GetForeColor()));
  8.增加属性页。在
  BEGIN_PROPPAGEIDS(CClockCtrl, 2)此时数目也得改成相应的数目
PROPPAGEID(CClockPropPage::guid)
PROPPAGEID(CLSID_CColorPropPage)
  END_PROPPAGEIDS(CClockCtrl)  OK~
  9.增加自定义属性：ClassWizard->AutoMation->Add Property加上一个变量m_interval,类型为short,对应外部变量为Interval。在CClockCtrl中增加OnIntervalChanged方法。添加如下代码：
   if(m_interval<0 || m_interval>6000)
{
  m_interval=1000;
}
else
{
  m_interval=m_interval/1000*1000;
  KillTimer(1);
  SetTimer(1,m_interval,NULL);
  BoundPropertyChanged(0x1);
}
   10.测试：Control->Invoke Methods
   11.将时间间隔加到属性页中，在资源视图中加入一文本框和编辑框。为EditBox关联成员变量，加入属性interval。
   12.增加方法：ClassWizard->AutoMation->Add Method->Hello加入代码 OK！在VB中可以调用此方法！
   void CClockCtrl::Hello() 
{
// TOD Add your dispatch handler code here
MessageBox("Hello world!");
}
   13.增加事件:ClassWizard->AutoMation->Add Events->Click
   14.增加自定义事件：ClassWizard->AutoMation->Add Events->NewMinute
      在新的一分钟到达时发出这个通知，在OnDraw()中写代码：
       CTime time=CTime::GetCurrentTime();
if(0==time.GetSecond())
{
  FireNewMinute();
}
   15.让Interval属性具有持久性。在CClockCtrl::DoPropExchange()中调用PX_short()方法，OK！
    PX_Short(pPX,"Interval",m_interval,1000);
   16.让Property Page和Property属性中的interval保持一致的方法：在OnIntervalChanged()中调用BoundPropertyChanged(0x1);
   17.希望控件在设计时间内不走动的方法：在OnTimer()中， if(AmbientUserMode())InvalidateControl();巧妙！
3.在VC中调用X控件
  1.新建ClockTest对话框应用程序
  2.点击右键->插入X控件->时钟控件
  3.Project->Add Component会生成CClock类。
  4.在CCLockTestDlg中增加CClock类的成员变量m_clock，然后可以动态创建一个这样的东东！
  5.试验Click(),NewMinute(),SetBkColor(),SetForeColor()方法和属性
  6.如何为动态创建的控件做事件响应呢？首先你得知道它的ID号，然后参考非动态的控件事件代码，呵。
第19课 DLL编程
1.DLL简介，动态库，静态库。动态库节约磁盘空间，静态库体积大。可以用多种语言编写DLL文件。动态库有两种加载方式：隐式调用和动态加裁！
2.新建一个DLL1的dll工程，加入一源文件名为dll1.cpp，加入add和subtract两个函数，注意此时须在函数名前加_declspec(dllexport)，并且编译。用dumpbi -exports dll1.dll查看其导出的函数，发现函数名字已经被改成了 ？add@@YAHHH@Z,这种现象叫做名字粉碎，是为了支持函数重载而做的。
3.编写一个程序测试DLL,工程名为DllTest，基于对话框的，放置两个按纽add和subtract,响应按纽消息，调用这个Dll的add和subtract函数。使用这两个函数前要先声明函数，//extern int add(int a,int b);
//extern int subtract(int a,int b);
还需要将Dll1.lib和Dll1.dll拷贝到当前目录下！另外还需要在Project->Setting->Link->Object/Library中加入Dll1.lib,此种方式为隐式调用！OK！用Dumpbin -imports DllTest.exe查看它的输入信息，可以看到它加载了dll1.dll。同时也可以用depends程序查看程序需要哪些dll文件！除了用extern外，还可以用//_declspec(dllimport) int add(int a,int b);
//_declspec(dllimport) int subtract(int a,int b);
告诉编译器，此函数是动态链接库中的函数，这样可以提高效率。
4.通常写Dll时在dll1.h中声明函数，然后在DllTest.h中包含这个头文件，另外会用一组宏来取代_declspec(dllimport)
Dll1.h
#ifdef DLL1_API
#else
#define DLL1_API extern "C" _declspec(dllimport)
#endif
DLL1_API int _stdcall add(int a,int b);
DLL1_API int _stdcall subtract(int a,int b);
Dll1.cpp的代码：
#define DLL1_API extern "C" _declspec(dllexport)
#include "Dll1.h"
#include <Windows.h>
#include <stdio.h>
int _stdcall add(int a,int b)
{
return a+b;
}
int _stdcall subtract(int a,int b)
{
return a-b;
}
5.在Dll1中加入类Point它有一个函数output(int a,intb),它的功能是在屏幕上输出x,y值。须包含头文件windows.h和stdio.h.然后在DllTest中加入一个按纽来测试这个函数！此时我们可以dumpbin来查看dll1.dll和dllTest.exe的导出导入情况。注意，也可以只导出类的某个函数。
6.我们希望导出的函数名不被改变，加extern "C"大写的C！即可，#define DLL1_API extern "C" _declspec(dllexport)，但它只能导出全局函数，不能导出类的成员函数，并且如果调用约定被改成了别的方式，此时函数名也被改变。所以这种方式不太好。
7.解决之道是用模块定义文件。
  1.新建dll2.dll工程；
  2.加dll2.cpp中写两个函数add和subtract
  3.在目录中新建dll2.def文件，增加到工程。
  4.在dll2.def中加入如下代码：
LIBRARY Dll2
EXPORTS
add
subtract
   5.编译后用dumpbin查看函数名是否被改变？
   6.测试，我们这次用动态加载的方法来调用dll文件。以前是用隐式链接的方法，嘿嘿。动态加载的好处是需要时再加载，可以提高执行的效率。代码如下：
HINSTANCE hInst;
hInst=LoadLibrary("Dll3.dll");
typedef int (/*_stdcall*/ *ADDPROC)(int a,int b);
//ADDPROC Add=(ADDPROC)GetProcAddress(hInst,"?add@@YAHHH@Z");
ADDPROC Add=(ADDPROC)GetProcAddress(hInst,MAKEINTRESOURCE(1));
if(!Add)
{
  MessageBox("获取函数地址失败！");
  return;
}
CString str;
str.Format("5+3=%d",Add(5,3));
MessageBox(str);
FreeLibrary(hInst);
    7.此时你改变调用约定，函数名不会被改变，但如果你加上_stdcall定义函数，调用时也需要加入_stdcall，否则会出错！
8.DllMain()是Dll的入口点，不过不是必须的。但在DllMain中不要做复杂的调用。为什么？因为DllMain加载时，某些核心Dll文件不一定已经被加载。
9.创建一个基于MFC的DLL工程，简介。
10.当不使用DLL时，调用FreeLibrary减少DLL的使用计数，释放DLL资源，减少系统负担。明白？
11.上面总结：1.*.def使函数名不改变；
             2.定义时为_stdcall,调用时也必须用_stdcall.
第20课 钩子与数据库编程
1.Hook简介：作用是拦截某些消息，关键函数是SetWindowsHookEX()
2.示例程序：
  1.新建一基于对话框工程，InnerHook,此过程的钩子是只拦截本进程的。
  2.在OnInitDialog()中添加代码：
   g_hWnd=m_hWnd;
g_hMouse=SetWindowsHookEx(WH_MOUSE,MouseProc,NULL,GetCurrentThreadId());设置了鼠标钩子
g_hKeyboard=SetWindowsHookEx(WH_KEYBOARD,KeyboardProc,NULL,GetCurrentThreadId());设置了键盘钩子
  3.完成钩子函数的编写：
HHOOK g_hKeyboard=NULL;
HHOOK g_hMouse;
HWND g_hWnd=NULL;

LRESULT CALLBACK MouseProc(
   int nCode,      // hook code
   WPARAM wParam,  // message identifier
   LPARAM lParam   // mouse coordinates
)
{
  return 1;
}

LRESULT CALLBACK KeyboardProc(
   int code,       // hook code
   WPARAM wParam,  // virtual-key code
   LPARAM lParam   // keystroke-message information
)
{
  //if(VK_SPACE==wParam || VK_RETURN==wParam)如果是空格键
  /*if(VK_F4==wParam && (1==(lParam>>29 & 1)))拦截ALT+F4按键！
   return 1;
  else
   return CallNextHookEx(g_hKeyboard,code,wParam,lParam);*/
  if(VK_F2==wParam)按F2时程序可以退出，这是留的后门。否则程序无法关闭，只能用任务管理器来关闭它了。
  {
   ::SendMessage(g_hWnd,WM_CLOSE,0,0);
   UnhookWindowsHookEx(g_hKeyboard);当程序退出时最好将钩子移除。
   UnhookWindowsHookEx(g_hMouse);
  }
  return 1;
}
3.编写一个屏屏蔽所有进程和所有线程的钩子程序。此时这个钩子必须安装在DLL中，然后被某个程序调用才行。
  1.新建一个DLL工程名为Hook
  2.增加Hook.cpp
  3.代码如下：
#include <windows.h>包含头文件

HHOOK g_hMouse=NULL;
HHOOK g_hKeyboard=NULL;

#pragma data_seg("MySec")新建了一个节，用于将下 面的这个变量设为全局共享。
HWND g_hWnd=NULL;这个变量是全局共享的。
#pragma data_seg()

//#pragma comment(linker,"/section:MySec,RWS")
/*HINSTANCE g_hInst;

BOOL WINAPI DllMain(
   HINSTANCE hinstDLL,  // handle to the DLL module
   DWORD fdwReason,     // reason for calling function
   LPVOID lpvReserved   // reserved
)
{
  g_hInst=hinstDLL;
}*/

LRESULT CALLBACK MouseProc(
   int nCode,      // hook code
   WPARAM wParam,  // message identifier
   LPARAM lParam   // mouse coordinates
)
{
  return 1;拦截了鼠标消息。
}

LRESULT CALLBACK KeyboardProc(
   int code,       // hook code
   WPARAM wParam,  // virtual-key code
   LPARAM lParam   // keystroke-message information
)
{
  if(VK_F2==wParam)如果是F2键，则退出。
  {
   SendMessage(g_hWnd,WM_CLOSE,0,0);
   UnhookWindowsHookEx(g_hMouse);当退出时将钩子卸掉。
   UnhookWindowsHookEx(g_hKeyboard);
  }
  return 1;
}

void SetHook(HWND hwnd)此函数设置了钩子。
{
  g_hWnd=hwnd;注意这种传递调用它的进程的句柄的方法，比较巧妙！
  g_hMouse=SetWindowsHookEx(WH_MOUSE,MouseProc,GetModuleHandle("Hook"),0);
  g_hKeyboard=SetWindowsHookEx(WH_KEYBOARD,KeyboardProc,GetModuleHandle("Hook"),0);
}
Hook.DEF的代码如下：
LIBRARY Hook
EXPORTS
SetHook  @2
SEGMENTS
MySec READ WRITE SHARED  也可以设置节的属性。
    4.新建一个工程调用此钩子函数。工程名为HookTest,基于对话框的。在OnInitDialog()中调用SetHook()，要事先声明_declspec(dllimport) void SetHook(HWND hwnd);
      然后在Project->Setting->Link->加入..\Hook\Debug\Hook.lib，并将Hook.Dll拷贝到当前目录。
int cxScreen,cyScreen;
cxScreen=GetSystemMetrics(SM_CXSCREEN);
cyScreen=GetSystemMetrics(SM_CYSCREEN);
SetWindowPos(&wndTopMost,0,0,cxScreen,cyScreen,SWP_SHOWWINDOW);将窗口保持在最前面。
SetHook(m_hWnd);
    5.DLL的调试方法，设置断点，然后运行时断点时，step into即可。
4.数据库编程
  1.ODBC，ADO简介：ADO可以认为是建立在ODBC上的。
   ADO的三个核心对象
Connection对象
   Connection对象表示了到数据库的连接，它管理应用程序和数据库之间的通信。 Recordset和Command对象都有一个ActiveConnection属性，该属性用来引用Connection对象。
Command对象
   Command对象被用来处理重复执行的查询，或处理需要检查在存储过程调用中的输出或返回参数的值的查询。
Recordset对象
   Recordset对象被用来获取数据。 Recordset对象存放查询的结果，这些结果由数据的行(称为记录)和列(称为字段)组成。每一列都存放在Recordset的Fields集合中的一个Field对象中。
  2.演示在VB中使用ADO的方法，方法比较简单，使用方便。另外在VB中演示了Connection和Command和Recordset的方法，用这三种方法都可以执行SQL语句。
  3.在VC中利用ADO访问数据库。
    1.新建一个基于对话框的工程，名为ADO。
    2.在对话框中放一ListBox和一个Button控件。
    3.在使用时须导入MSADO15.dll，方法是在StdAfx.h中#import "D:\Program Files\Common Files\System\ado\msado15.dll" no_namespace rename("EOF","rsEOF")
    至少于将EOF改名为rsEOF，是为了避免与文件中的EOF重名。然后编译程序，将产生的debug目录下的两个文件MSADO15.tlh和MSADO15.tli加到工程中，其目的只是方便我们查看而已。并不是编译需要它。
    ADO也是COM组件，须初始化COM库方法是CoInitialize(NULL);使用完后须CoUninitialize();
    代码如下：
    void CAdoDlg::OnBtnQuery() 
{
// TOD Add your control notification handler code here
CoInitialize(NULL);初始化
_ConnectionPtr pConn(__uuidof(Connection));产生connection智能指针
_RecordsetPtr pRst(__uuidof(Recordset));产生recordset智能指针
_CommandPtr pCmd(__uuidof(Command));产生command智能指针
pConn->ConnectionString="Provider=SQLOLEDB.1;Persist Security Info=False;User ID=sa;Initial Catalog=pubs";数据库信息
pConn->Open("","","",adConnectUnspecified);打开数据库
//pRst=pConn->Execute("select * from authors",NULL,adCmdText);用记录集查询数据
//pRst->Open("select * from authors",_variant_t((IDispatch*)pConn),
// adOpenDynamic,adLockOptimistic,adCmdText);
pCmd->put_ActiveConnection(_variant_t((IDispatch*)pConn));
pCmd->CommandText="select * from authors";用这种方法也可以查询数据
pRst=pCmd->Execute(NULL,NULL,adCmdText);
while(!pRst->rsEOF)将查询到的数据加到列表框咯。
{
  ((CListBox*)GetDlgItem(IDC_LIST1))->AddString(
   (_bstr_t)pRst->GetCollect("au_lname"));
  pRst->MoveNext();
}

pRst->Close();
pConn->Close();
pCmd.Release();
pRst.Release();
pConn.Release();
CoUninitialize();
}
至此20课笔记全部记完，关于数据库的比较简单些。

