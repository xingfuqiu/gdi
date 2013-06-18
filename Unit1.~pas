unit Unit1;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, jpeg, ExtCtrls, uU8Button, GDIPAPI, GDIPOBJ, Menus, StdCtrls;

type
  TForm1 = class(TForm)
    Image1: TImage;
    PopupMenu1: TPopupMenu;
    c1: TMenuItem;
    ChangeSkin1: TMenuItem;
    Close1: TMenuItem;
    procedure FormCreate(Sender: TObject);
    procedure FormMouseDown(Sender: TObject; Button: TMouseButton;
      Shift: TShiftState; X, Y: Integer);
    procedure c1Click(Sender: TObject);
    procedure ChangeSkin1Click(Sender: TObject);
    procedure Close1Click(Sender: TObject);
  private
    m_Blend: BLENDFUNCTION;
    procedure SetTransparent(lpSkinFile: WideString; nTran: integer);
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.dfm}

procedure TForm1.FormCreate(Sender: TObject);
begin
  BorderStyle := bsNone;
  m_Blend.BlendOp := AC_SRC_OVER; //   the   only   BlendOp   defined   in   Windows   2000
  m_Blend.BlendFlags := 0; //   Must   be   zero
  m_Blend.AlphaFormat := AC_SRC_ALPHA; //This   flag   is   set   when   the   bitmap   has   an   Alpha   channel
  m_Blend.SourceConstantAlpha := 255;
//  if (FileExists(ExtractFilePath(ParamStr(0)) + 'Security - Alert.png')) then
//    SetTransparent(WideString(ExtractFilePath(ParamStr(0)) + 'Security - Alert.png'), 100);
  if (FileExists('C:\1.png')) then
    SetTransparent(WideString('C:\1.png'), 100);
  //   Stay   on   top
  SetWindowPos(Handle, HWND_TOPMOST, 0, 0, 0, 0, SWP_NOMOVE or SWP_NOSIZE);
  //SetTransparent('C:\1.png', 255);
end;

procedure TForm1.SetTransparent(lpSkinFile: WideString; nTran: integer);
var
  GPImage: TGPImage;
  GPGraph: TGPGraphics;
  m_Image, m_Button, m_Pic: TGPImage;
  m_hdcMemory: HDC;
  hdcScreen: HDC;
  hBMP: HBITMAP;
  sizeWindow: SIZE;
  rct: TRECT;
  ptSrc: TPOINT;
begin
  //   Use   GDI+   load   image
  GPImage := TGPImage.Create();
  m_Image := GPImage.FromFile(lpSkinFile);
  m_Button := TGPImage.Create();
  m_Button := GPImage.FromFile('C:\2.png');
  m_Pic := TGPImage.Create();
  m_Pic := GPImage.FromFile('C:\pic.jpg');

  //   Create   Compatible   Bitmap
  hdcScreen := GetDC(0);
  m_hdcMemory := CreateCompatibleDC(hdcScreen);
  hBMP := CreateCompatibleBitmap(hdcScreen, m_Image.GetWidth(), m_Image.GetHeight());
  SelectObject(m_hdcMemory, hBMP);
  //   Alpha   Value
  if (nTran < 0) or (nTran > 100) then
    nTran := 100;
  m_Blend.SourceConstantAlpha := round(nTran * 2.55); //   1~255
  GetWindowRect(Handle, rct);
  GPGraph := TGPGraphics.Create(m_hdcMemory);
  //±³¾°
  GPGraph.DrawImage(m_Image, 0, 0, m_Image.GetWidth(), m_Image.GetHeight());
  //°´Å¥
  GPGraph.DrawImage(m_Button, m_Image.GetWidth()-m_Button.GetWidth(), 0, m_Button.GetWidth(), m_Button.GetHeight());
  //Í¼Æ¬
  GPGraph.SetInterpolationMode(InterpolationModeHighQualityBicubic);
  GPGraph.DrawImage(m_Pic, MakeRect(0.1*m_Image.GetWidth(), 0.1*m_Image.GetHeight(), 0.8*m_Image.GetWidth(), 0.8*m_Image.GetHeight()), 0, 0, m_Pic.GetWidth(), m_Pic.GetHeight(), UnitPixel);

  sizeWindow.cx := m_Image.GetWidth();
  sizeWindow.cy := m_Image.GetHeight();
  ptSrc.x := 0;
  ptSrc.y := 0;
  //   Set   Window   style
  SetWindowLong(Handle, GWL_EXSTYLE, GetWindowLong(Handle, GWL_EXSTYLE) or WS_EX_LAYERED);
  //   perform   the   alpha   blend
  UpdateLayeredWindow(Handle, hdcScreen, nil,@sizeWindow, m_hdcMemory, @ptSrc, 0, @m_Blend, ULW_ALPHA);
  //Release   resources
  GPGraph.ReleaseHDC(m_hdcMemory);
  ReleaseDC(0, hdcScreen);
  hdcScreen := 0;
  DeleteObject(hBMP);
  DeleteDC(m_hdcMemory);
  m_hdcMemory := 0;
  m_Image.Free;
  GPGraph.Free;
end;

procedure TForm1.FormMouseDown(Sender: TObject; Button: TMouseButton;
  Shift: TShiftState; X, Y: Integer);
begin
//  if (Button = mbLeft) then
//  begin
    ReleaseCapture();
    //Perform(WM_SYSCOMMAND, SC_MOVE or HTCAPTION, 0);
    SendMessage(Handle, WM_NCLBUTTONDOWN, HTCAPTION, 0);

//  end;
end;

procedure TForm1.c1Click(Sender: TObject);
var
  mi: TMenuItem;
  WindowPos: HWND;
begin
  mi := Sender as TMenuItem;
  mi.Checked := not mi.Checked;
  if mi.Checked then
    WindowPos := HWND_TOPMOST
  else
    WindowPos := HWND_NOTOPMOST;
  SetWindowPos(Handle, WindowPos,0, 0, 0, 0, SWP_NOMOVE or SWP_NOSIZE);
end;

procedure TForm1.ChangeSkin1Click(Sender: TObject);
var
  dlgOpen: TOpenDialog;
begin
  dlgOpen := TOpenDialog.Create(Self);
  dlgOpen.Filter := 'PNG   file(*.png)|*.png';
  if (dlgOpen.Execute()) then
  begin
    SetTransparent(WideString(dlgOpen.FileName), 100);
    Invalidate();
  end;
  dlgOpen.Free;
end;

procedure TForm1.Close1Click(Sender: TObject);
begin
  Close;
end;

end.
