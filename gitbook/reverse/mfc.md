### 第一章

[![ppDJQBT.png](https://s1.ax1x.com/2023/03/25/ppDJQBT.png)](https://imgse.com/i/ppDJQBT)

[![ppDJIUg.png](https://s1.ax1x.com/2023/03/25/ppDJIUg.png)](https://imgse.com/i/ppDJIUg)



从win32转换成mfc

[![ppDKfxO.png](https://s1.ax1x.com/2023/03/25/ppDKfxO.png)](https://imgse.com/i/ppDKfxO)

第一个MFC

[![ppDK2Px.png](https://s1.ax1x.com/2023/03/25/ppDK2Px.png)](https://imgse.com/i/ppDK2Px)

框架类

[![ppDK7dA.png](https://s1.ax1x.com/2023/03/25/ppDK7dA.png)](https://imgse.com/i/ppDK7dA)



自定义程序类-弄了个空的构造函数和重写了一个虚函数

[![ppDKHII.png](https://s1.ax1x.com/2023/03/25/ppDKHII.png)](https://imgse.com/i/ppDKHII)

[![ppDKxsg.png](https://s1.ax1x.com/2023/03/25/ppDKxsg.png)](https://imgse.com/i/ppDKxsg)

[![ppDKvQS.png](https://s1.ax1x.com/2023/03/25/ppDKvQS.png)](https://imgse.com/i/ppDKvQS)

~~~c++
#include <afxwin.h>

class CMyFrameWind : public CFrameWnd {

};

class CMyWinApp : public CWinApp {
public:
    CMyWinApp() {

    }
    virtual BOOL InitInstance() {
        CMyFrameWind* pFrame = new CMyFrameWind;
        pFrame->Create(NULL, "MFCBase");
        this->m_pMainWnd = pFrame;
        pFrame->ShowWindow(SW_SHOW);
        pFrame->UpdateWindow();
        return TRUE;

    }
};

CMyWinApp theAPP;
~~~

[![ppDMy6S.png](https://s1.ax1x.com/2023/03/25/ppDMy6S.png)](https://imgse.com/i/ppDMy6S)



##### 入口函数

[![ppDM2wj.png](https://s1.ax1x.com/2023/03/25/ppDM2wj.png)](https://imgse.com/i/ppDM2wj)

爆破点优先入口函数

我们打个断点，进入F11，就到了本类构造了[![ppDQQBQ.png](https://s1.ax1x.com/2023/03/25/ppDQQBQ.png)](https://imgse.com/i/ppDQQBQ)

再F11就到了基类构造

~~~c++
AFX_MODULE_STATE* pModuleState = _AFX_CMDTARGET_GETSTATE();
// 获取全局变量的地址(三个中的一个)->就是在获取这个aaa全局变量的地址

AFX_MODULE_STATE aaa; // 当前程序模块状态信息
AFX_MODULE_THREAD_STATE bbb; // 当前程序线程状态信息

AFX_MODULE_THREAD_STATE* pThreadState = pModuleState->m_thread;
// 获取全局变量的地址(第二个)

猜测程序bbb的地址应该是在aaa的一个成员里
    
pThreadState->m_pCurrentWinThread = this;    

~~~

[![ppDQggK.png](https://s1.ax1x.com/2023/03/25/ppDQggK.png)](https://imgse.com/i/ppDQggK)

将自己定义的全局对象爆破点theAPP的地址，保存在bbb的一个成员当中

~~~c++
	ASSERT(AfxGetThread() == NULL);
	pThreadState->m_pCurrentWinThread = this;
	ASSERT(AfxGetThread() == this);
~~~

父类构造->子类构造->入口函数



我们发现重写的这个虚函数是在创建和显示窗口，我们的窗口被入口函数调用了，

[![ppDakfe.png](https://s1.ax1x.com/2023/03/25/ppDakfe.png)](https://imgse.com/i/ppDakfe)

### 第二章-窗口和消息

##### 钩子

有优先钩取消息的权力

~~~
//钩子类型，有键盘，鼠标等钩子，可以写WH_CBT[窗口创建消息钩子]
//钩子处理函数，是我们自定义的，由系统去调用，名字放在第二个参数
//对应进程的句柄，如果为空就是所有进程
//对应进程的某线程，如果是0就是所有的线程

// 狗钩子码和钩子类型是一一对应的，wParam就是刚刚创建的窗口句柄
~~~

[![ppD1QFs.png](https://s1.ax1x.com/2023/03/25/ppD1QFs.png)](https://imgse.com/i/ppD1QFs)

~~~
// 第二个参数是可以更改窗口处理函数，如果是其他的就是风格等
// 第三个就是新的窗口处理函数地址
~~~



[![ppD120e.png](https://s1.ax1x.com/2023/03/25/ppD120e.png)](https://imgse.com/i/ppD120e)

[![ppD1T6f.png](https://s1.ax1x.com/2023/03/25/ppD1T6f.png)](https://imgse.com/i/ppD1T6f)

##### 处理消息

~~~c++
#include <afxwin.h>
class CMyFrameWnd : public CFrameWnd {
public:
	virtual LRESULT WindowProc(UINT msgID, WPARAM wParam, LPARAM);
};
LRESULT CMyFrameWnd::WindowProc(UINT msgID, WPARAM wParam, LPARAM lParam) {
	//此函数内部的this为pFrame
	switch (msgID) {
	case WM_CREATE:
		AfxMessageBox("WM_CREATE消息被处理");
		break;
	case WM_PAINT:
	{
		PAINTSTRUCT ps = { 0 };
		HDC hdc = ::BeginPaint(this->m_hWnd, &ps);
		::TextOut(hdc, 100, 100, "hello", 5);
		::EndPaint(m_hWnd, &ps);
	}
	break;
	}
	return CFrameWnd::WindowProc(msgID, wParam, lParam);
}
class CMyWinApp : public CWinApp {
public:
	virtual BOOL InitInstance();
};

CMyWinApp theApp;//爆破点

BOOL CMyWinApp::InitInstance() {
	CMyFrameWnd* pFrame = new CMyFrameWnd;
	pFrame->Create(NULL, "MFCCreate");
	m_pMainWnd = pFrame;
	pFrame->ShowWindow(SW_SHOW);
	pFrame->UpdateWindow();
	return TRUE;
}

~~~



##### 消息映射机制

[![pprYsHK.png](https://s1.ax1x.com/2023/03/26/pprYsHK.png)](https://imgse.com/i/pprYsHK)

[![pprYR9H.png](https://s1.ax1x.com/2023/03/26/pprYR9H.png)](https://imgse.com/i/pprYR9H)

~~~c++
#include <afxwin.h>
class CMyFrameWnd : public CFrameWnd{
//	DECLARE_MESSAGE_MAP()
protected: 
	static const AFX_MSGMAP* PASCAL GetThisMessageMap(); 
	virtual const AFX_MSGMAP* GetMessageMap() const; 
public:
	LRESULT OnCreate( WPARAM wParam, LPARAM lParam );
};
//BEGIN_MESSAGE_MAP(CMyFrameWnd, CFrameWnd)
//	ON_MESSAGE( WM_CREATE, OnCreate )
//END_MESSAGE_MAP()

 
const AFX_MSGMAP* CMyFrameWnd::GetMessageMap() const 
{ 
	return GetThisMessageMap(); 
} 
const AFX_MSGMAP* PASCAL CMyFrameWnd::GetThisMessageMap() 
{ 				   
		static const AFX_MSGMAP_ENTRY _messageEntries[] =  
		{
			{ WM_CREATE, 0, 0, 0, AfxSig_lwl, (AFX_PMSG)(AFX_PMSGW) 
				(static_cast< LRESULT (AFX_MSG_CALL CWnd::*)(WPARAM, LPARAM) > (&OnCreate)) },

			{0, 0, 0, 0, AfxSig_end, (AFX_PMSG)0 } 
		}; 
		static const AFX_MSGMAP messageMap = { &CFrameWnd::GetThisMessageMap, &_messageEntries[0] }; 
		return &messageMap; 
}								  
  
LRESULT CMyFrameWnd::OnCreate( WPARAM wParam, LPARAM lParam ){
	AfxMessageBox( "WM_CREATE" );
	return 0;
}

class CMyWinApp : public CWinApp{
public:
	virtual BOOL InitInstance( );
};
CMyWinApp theApp;//爆破点
BOOL CMyWinApp::InitInstance( ){
		CMyFrameWnd* pFrame = new CMyFrameWnd;
		pFrame->Create(NULL, "MFCCreate");
		m_pMainWnd = pFrame;
		pFrame->ShowWindow( SW_SHOW );
		pFrame->UpdateWindow( );
		return TRUE;
}
~~~



~~~
protected: \
	static const AFX_MSGMAP* PASCAL GetThisMessageMap(); \
	virtual const AFX_MSGMAP* GetMessageMap() const; \
~~~

[![pprteDx.png](https://s1.ax1x.com/2023/03/26/pprteDx.png)](https://imgse.com/i/pprteDx)[![pprtmb6.png](https://s1.ax1x.com/2023/03/26/pprtmb6.png)](https://imgse.com/i/pprtmb6)



##### 消息的分类

[![pprtBGQ.png](https://s1.ax1x.com/2023/03/26/pprtBGQ.png)](https://imgse.com/i/pprtBGQ)

~~~c++
#include <afxwin.h>
#define WM_MYMESSAGE WM_USER+1001
class CMyFrameWnd : public CFrameWnd{
	DECLARE_MESSAGE_MAP()
public:
	int OnCreate( LPCREATESTRUCT pcs);
	void OnPaint( );
	void OnMouseMove( UINT nKey, CPoint pt );
	LRESULT OnMyMessage( WPARAM wParam, LPARAM lParam );
	int m_x;
	int m_y;
};
BEGIN_MESSAGE_MAP(CMyFrameWnd, CFrameWnd)
	ON_WM_CREATE()
	ON_WM_PAINT()
	ON_WM_MOUSEMOVE()
	ON_MESSAGE( WM_MYMESSAGE,  OnMyMessage)
END_MESSAGE_MAP()

LRESULT CMyFrameWnd::OnMyMessage( WPARAM wParam, LPARAM lParam ){
	CString str;
	str.Format( "wParam=%d,lParam=%d", wParam, lParam );
	AfxMessageBox( str );
	return 0;
}
void CMyFrameWnd::OnMouseMove( UINT nKey, CPoint pt){
	m_x = pt.x;
	m_y = pt.y;
	::InvalidateRect( this->m_hWnd, NULL, TRUE );
}
void CMyFrameWnd::OnPaint( ){
	PAINTSTRUCT ps = { 0 };
	HDC hdc = ::BeginPaint( this->m_hWnd, &ps );
	::TextOut( hdc, m_x, m_y, "hello", 5);
	::EndPaint( m_hWnd, &ps );
}
int CMyFrameWnd::OnCreate( LPCREATESTRUCT pcs ){
	AfxMessageBox("WM_CREATE消息被处理");
	::PostMessage( this->m_hWnd, WM_MYMESSAGE, 1, 2 );
	return CFrameWnd::OnCreate( pcs );
}
class CMyWinApp : public CWinApp{
public:
	virtual BOOL InitInstance( );
};
CMyWinApp theApp;//爆破点
BOOL CMyWinApp::InitInstance( ){
		CMyFrameWnd* pFrame = new CMyFrameWnd;
		pFrame->Create(NULL, "MFCCreate");
		m_pMainWnd = pFrame;
		pFrame->ShowWindow( SW_SHOW );
		pFrame->UpdateWindow( );
		return TRUE;
}
~~~

#### MFC菜单

[![pprNpQA.png](https://s1.ax1x.com/2023/03/26/pprNpQA.png)](https://imgse.com/i/pprNpQA)[![pprNko8.png](https://s1.ax1x.com/2023/03/26/pprNko8.png)](https://imgse.com/i/pprNko8)

[![pprNVJg.png](https://s1.ax1x.com/2023/03/26/pprNVJg.png)](https://imgse.com/i/pprNVJg)

~~~c++
#include <afxwin.h>
#include "resource.h"
class CMyFrameWnd : public CFrameWnd{	
	DECLARE_MESSAGE_MAP( )
public:
	afx_msg int OnCreate( LPCREATESTRUCT pcs );
	afx_msg void OnNew( );
	afx_msg void OnInitMenuPopup( CMenu *pPopup, UINT nPos, BOOL i );
	afx_msg void OnContextMenu( CWnd* pWnd, CPoint pt );
public:
	CMenu menu;
};
BEGIN_MESSAGE_MAP( CMyFrameWnd, CFrameWnd )
//	ON_COMMAND( ID_NEW, OnNew )
	ON_WM_CREATE( )
	ON_WM_INITMENUPOPUP()
	ON_WM_CONTEXTMENU( )
END_MESSAGE_MAP( )
void CMyFrameWnd::OnContextMenu( CWnd* pWnd, CPoint pt ){
//	HMENU hPopup = ::GetSubMenu(menu.m_hMenu,0);
//	::TrackPopupMenu( hPopup, TPM_LEFTALIGN|TPM_TOPALIGN, pt.x, pt.y,
//										0, this->m_hWnd, NULL );
	CMenu* pPopup = menu.GetSubMenu(0);
	pPopup->TrackPopupMenu( TPM_LEFTALIGN|TPM_TOPALIGN, pt.x, pt.y, this );
}
void CMyFrameWnd::OnInitMenuPopup( CMenu* pPopup, UINT nPos, BOOL i){
//	pPopup->CheckMenuItem( ID_NEW, MF_CHECKED );
	::CheckMenuItem( pPopup->m_hMenu, ID_NEW, MF_CHECKED );
}
void CMyFrameWnd::OnNew( ){
	AfxMessageBox( "框架类处理了新建菜单项被点击" );
}
int CMyFrameWnd::OnCreate( LPCREATESTRUCT pcs ){
	menu.LoadMenu( IDR_MENU1 );
//	this->SetMenu( &menu );
	::SetMenu( this->m_hWnd, menu.m_hMenu);
	return CFrameWnd::OnCreate( pcs );
}
class CMyWinApp : public CWinApp{
	DECLARE_MESSAGE_MAP()
public:
	virtual BOOL InitInstance( );
	afx_msg void OnNew( );
};
BEGIN_MESSAGE_MAP(CMyWinApp, CWinApp)
	ON_COMMAND( ID_NEW , OnNew )
END_MESSAGE_MAP( )
void CMyWinApp::OnNew( ){
	AfxMessageBox( "应用程序类处理了新建被点击" );
}
BOOL CMyWinApp::InitInstance( ){
		CMyFrameWnd* pFrame = new CMyFrameWnd;
		pFrame->Create(NULL, "MFCCreate");
		m_pMainWnd = pFrame;
		pFrame->ShowWindow( SW_SHOW );
		pFrame->UpdateWindow( );
		return TRUE;
}
CMyWinApp theApp;

~~~





~~~c++
// WindowsProject2.cpp : 定义应用程序的入口点。
//

#include "framework.h"
#include "WindowsProject2.h"

#define MAX_LOADSTRING 100

// 全局变量:
HINSTANCE hInst;                                // 当前实例
WCHAR szTitle[MAX_LOADSTRING];                  // 标题栏文本
WCHAR szWindowClass[MAX_LOADSTRING];            // 主窗口类名

// 此代码模块中包含的函数的前向声明:
ATOM                MyRegisterClass(HINSTANCE hInstance);
BOOL                InitInstance(HINSTANCE, int);
LRESULT CALLBACK    WndProc(HWND, UINT, WPARAM, LPARAM);
INT_PTR CALLBACK    About(HWND, UINT, WPARAM, LPARAM);

int APIENTRY wWinMain(_In_ HINSTANCE hInstance,
                     _In_opt_ HINSTANCE hPrevInstance,
                     _In_ LPWSTR    lpCmdLine,
                     _In_ int       nCmdShow)
{
    UNREFERENCED_PARAMETER(hPrevInstance);
    UNREFERENCED_PARAMETER(lpCmdLine);

    // TODO: 在此处放置代码。

    // 初始化全局字符串
    LoadStringW(hInstance, IDS_APP_TITLE, szTitle, MAX_LOADSTRING);
    LoadStringW(hInstance, IDC_WINDOWSPROJECT2, szWindowClass, MAX_LOADSTRING);
    MyRegisterClass(hInstance);

    // 执行应用程序初始化:
    if (!InitInstance (hInstance, nCmdShow))
    {
        return FALSE;
    }

    HACCEL hAccelTable = LoadAccelerators(hInstance, MAKEINTRESOURCE(IDC_WINDOWSPROJECT2));

    MSG msg;

    // 主消息循环:
    while (GetMessage(&msg, nullptr, 0, 0))
    {
        if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg))
        {
            TranslateMessage(&msg);
            DispatchMessage(&msg);
        }
    }

    return (int) msg.wParam;
}



//
//  函数: MyRegisterClass()
//
//  目标: 注册窗口类。
//
ATOM MyRegisterClass(HINSTANCE hInstance)
{
    WNDCLASSEXW wcex;

    wcex.cbSize = sizeof(WNDCLASSEX);

    wcex.style          = CS_HREDRAW | CS_VREDRAW;
    wcex.lpfnWndProc    = WndProc;
    wcex.cbClsExtra     = 0;
    wcex.cbWndExtra     = 0;
    wcex.hInstance      = hInstance;
    wcex.hIcon          = LoadIcon(hInstance, MAKEINTRESOURCE(IDI_WINDOWSPROJECT2));
    wcex.hCursor        = LoadCursor(nullptr, IDC_ARROW);
    wcex.hbrBackground  = (HBRUSH)(COLOR_WINDOW+1);
    wcex.lpszMenuName   = MAKEINTRESOURCEW(IDC_WINDOWSPROJECT2);
    wcex.lpszClassName  = szWindowClass;
    wcex.hIconSm        = LoadIcon(wcex.hInstance, MAKEINTRESOURCE(IDI_SMALL));

    return RegisterClassExW(&wcex);
}

//
//   函数: InitInstance(HINSTANCE, int)
//
//   目标: 保存实例句柄并创建主窗口
//
//   注释:
//
//        在此函数中，我们在全局变量中保存实例句柄并
//        创建和显示主程序窗口。
//
BOOL InitInstance(HINSTANCE hInstance, int nCmdShow)
{
   hInst = hInstance; // 将实例句柄存储在全局变量中

   HWND hWnd = CreateWindowW(szWindowClass, szTitle, WS_OVERLAPPEDWINDOW,
      CW_USEDEFAULT, 0, CW_USEDEFAULT, 0, nullptr, nullptr, hInstance, nullptr);

   if (!hWnd)
   {
      return FALSE;
   }

   ShowWindow(hWnd, nCmdShow);
   UpdateWindow(hWnd);

   return TRUE;
}

//
//  函数: WndProc(HWND, UINT, WPARAM, LPARAM)
//
//  目标: 处理主窗口的消息。
//
//  WM_COMMAND  - 处理应用程序菜单
//  WM_PAINT    - 绘制主窗口
//  WM_DESTROY  - 发送退出消息并返回
//
//
LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    switch (message)
    {
    case WM_COMMAND:
        {
            int wmId = LOWORD(wParam);
            // 分析菜单选择:
            switch (wmId)
            {
            case IDM_ABOUT:
                DialogBox(hInst, MAKEINTRESOURCE(IDD_ABOUTBOX), hWnd, About);
                break;
            case IDM_EXIT:
                DestroyWindow(hWnd);
                break;
            default:
                return DefWindowProc(hWnd, message, wParam, lParam);
            }
        }
        break;
    case WM_PAINT:
        {
            PAINTSTRUCT ps;
            HDC hdc = BeginPaint(hWnd, &ps);
            // TODO: 在此处添加使用 hdc 的任何绘图代码...
            EndPaint(hWnd, &ps);
        }
        break;
    case WM_DESTROY:
        PostQuitMessage(0);
        break;
    default:
        return DefWindowProc(hWnd, message, wParam, lParam);
    }
    return 0;
}

// “关于”框的消息处理程序。
INT_PTR CALLBACK About(HWND hDlg, UINT message, WPARAM wParam, LPARAM lParam)
{
    UNREFERENCED_PARAMETER(lParam);
    switch (message)
    {
    case WM_INITDIALOG:
        return (INT_PTR)TRUE;

    case WM_COMMAND:
        if (LOWORD(wParam) == IDOK || LOWORD(wParam) == IDCANCEL)
        {
            EndDialog(hDlg, LOWORD(wParam));
            return (INT_PTR)TRUE;
        }
        break;
    }
    return (INT_PTR)FALSE;
}

~~~

[![pprUFXR.png](https://s1.ax1x.com/2023/03/26/pprUFXR.png)](https://imgse.com/i/pprUFXR)

.

































