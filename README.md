# 펜 프로그램 내용 및 실행화면

## 수정 내용

### xaml

xmlns:canvas="using:Microsoft.Graphics.Canvas.UI.Xaml" 추가: Microsoft.Graphics.Canvas.UI.Xaml.h 파일의 사용을 가능하게 한다.

<Grid>
    <canvas:CanvasControl 
        PointerReleased="CanvasControl_PointerReleased"
        PointerPressed="CanvasControl_PointerPressed"
        PointerMoved="CanvasControl_PointerMoved"
        Draw="CanvasControl_Draw"
        ClearColor="BlueViolet"
        />

</Grid>
ClearColor를 제외한 각 줄은 처리기 추가 선택지를 클릭해 처리기를 새로 만들도록 한다.

### h

#include <winrt/Microsoft.Graphics.Canvas.UI.Xaml.h>
#include <winrt/Microsoft.UI.Input.h>
#include <winrt/Microsoft.UI.Xaml.Input.h>

위에서부터 캔버스 요소 사용, 마우스 입력 사용이다.

struct MainWindow : MainWindowT<MainWindow> 내부:

bool flag;
float px, py;
std::vector<float> vx;
std::vector<float> vy;

좌표 표시를 위한 요소들이다.


### cpp
using namespace Microsoft::Graphics::Canvas::UI::Xaml;
using namespace Microsoft::UI::Input;
using namespace Microsoft::UI::Xaml::Input;
struct winrt::Windows::UI::Color col = winrt::Microsoft::UI::Colors::Green();

헤더파일에 이미 include 되어있어 기능 사용은 가능하지만, 일일이 어느 헤더 파일의 기능인지를 적어줘야 하기 때문에 편의성 확보를 위해 using namespace .. 를 이용해 줄여준다.
가장 마지막의 구조체도 간략화의 일환이다. 색을 변수 col에 지정해 col을 넣는 것 만으로 색을 가져올 수 있게 했다.

MainWindow::MainWindow() 내부:
flag = false;
px = py = 100;

플래그와 좌표 변수 초기화.


CanvasControl_PointerReleased 내부:
flag = false;
px = py = 0.0;
vx.push_back(px);
vy.push_back(py);

초기(마우스 조작중이 아닐 경우) 좌표값을 설정하고 벡터 변수에 대입한다.


CanvasControl_PointerPressed 내부:
flag = true;



CanvasControl_PointerMoved 내부:
CanvasControl canvas = sender.as<CanvasControl>();
px = e.GetCurrentPoint(canvas).Position().X;
py = e.GetCurrentPoint(canvas).Position().Y;

if (flag) {

    vx.push_back(px);
    vy.push_back(py);
    canvas.Invalidate();
}

좌표값 변수에 실제 마우스의 위치 좌표값을 대입한다. 그 값을 벡터 변수에 대입하여 마우스의 움직임에 따라갈 수 있게 한다.



CanvasControl_Draw 내부:
CanvasControl canvas = sender.as<CanvasControl>();
int n = vx.size();
if (n <= 1) return;

for (int i = 1; i < n; i++) {
    if (vx[i] == 0 && vy[i] == 0) {
        i++;
        continue;
    }
    args.DrawingSession().DrawLine(vx[i - 1], vy[i - 1], vx[i], vy[i], col, 16);
    args.DrawingSession().FillEllipse(vx[i - 1], vy[i - 1], 16, 16, col);
}

변수의 값이 범위를 벗어나는 것을 조심하면서 마우스의 움직임에 따라 선을 그리도록 해준다. 


### 실행화면
![스크린샷 2023-11-20 103341](https://github.com/CommercialCrew/MyPen/assets/101386134/1f77fb2e-8c5a-4ad2-ad02-be4e95b19bd6)

정상적으로 배경 색과 펜 색이 적용되었다.

### 소감
매번 새로운 형식의 프로그램 개발을 배울 때 마다 나오는 펜 만들기지만 그렇기 때문에 이미 어떤 부분이 필요한지 알고있어 각 부분을 이전에 배웠던 내용으로 만들었던 펜 프로그램에 대입하게 돼서 더욱 빨리 배워갈 수 있었다. 
