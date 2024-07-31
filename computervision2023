#include <opencv2/opencv.hpp> 
#include <iostream>
#include <GL/glut.h>
#include <GL/glui.h>
#include "OpenFileDialog.h"
#include "SaveFileDialog.h"

using namespace cv;
using namespace std;

int main_window; //glui 생성에 사용
int num; //라디오 버튼 enable, disable에 사용
int width, height; //resize 할때 너비 높이에 사용
int start_num = 0; //초기 상태
int gray = 0; //To gray checkbox에 사용
int gaussian = 0; //Gaussian checkbox에 사용
int histogram = 0; //Histogram checkbox에 사용
int num2; //라디오 버튼 none, canny, threshold에 사용
int thresh_value; //threshold 사용할 때에 임계값에 사용
int save = 0, savesave = 0; //save checkbox에 사용
int fourcc = VideoWriter::fourcc('X', 'V', 'I', 'D');//코덱
//int fourcc = VideoWriter::fourcc('M', 'J', 'P', 'G');//코덱
int fps = 15; //15프레임

String Filename;
GLUI_Button* FileOpen;
GLUI_Button* Start;
Mat frame, dst;
Mat file, file_dst;
VideoCapture cap(0), video;
GLUI_Spinner* spin_W;
GLUI_Spinner* spin_H;
vector<Mat> channels;
VideoWriter output;


void rg1_option(int id)
{
	if (num == 0)
	{
		FileOpen->enable();
		Start->disable();
	}
	else if (num == 1)
	{
		FileOpen->disable();
		Start->enable();
	}
}
void open() {
	if (start_num == 1) {
		video >> file;
		video >> file_dst;
		resize(file_dst, file_dst, Size(width, height));
		if (gray == 1) {
			cvtColor(file_dst, file_dst, COLOR_BGR2GRAY);
			if (histogram == 1) equalizeHist(file_dst, file_dst);
			if (gaussian == 1) GaussianBlur(file_dst, file_dst, Size(7, 7), 0, 0);
			if (num2 == 1) Canny(file_dst, file_dst, 27, 3, 3);
			else if (num2 == 2) threshold(file_dst, file_dst, thresh_value, 255, THRESH_BINARY);
		}
		else {
			if (histogram == 1) {
				cvtColor(file_dst, file_dst, COLOR_BGR2HSV);
				split(file_dst, channels);
				equalizeHist(channels[2], channels[2]);
				merge(channels, file_dst);
				cvtColor(file_dst, file_dst, COLOR_HSV2BGR);
			}
		}
		if (gaussian == 1) GaussianBlur(file_dst, file_dst, Size(7, 7), 0, 0);
		if (num2 == 2) threshold(file_dst, file_dst, thresh_value, 255, THRESH_BINARY);

		imshow("file", file);
		imshow("file_dst", file_dst);

		//여기서부터 저장할 때
		if (savesave == 1) {
			if (gray == 0) output.write(file_dst);
			else {
				cvtColor(file_dst, file_dst, COLOR_GRAY2RGB);
				output.write(file_dst);
			}
		}

		waitKey(30);
	}		
	if (start_num == 2) {
		cap >> frame;
		cap >> dst;
		resize(dst, dst, Size(width, height));
		if (gray == 1) {
			cvtColor(dst, dst, COLOR_BGR2GRAY);
			if (histogram == 1) equalizeHist(dst, dst);
			if (gaussian == 1) GaussianBlur(dst, dst, Size(7, 7), 0, 0);
			if (num2 == 1) Canny(dst, dst, 27, 3, 3);
			else if (num2 == 2) threshold(dst, dst, thresh_value, 255, THRESH_BINARY);
			
		}
		else {
			if (histogram == 1) {
				cvtColor(dst, dst, COLOR_BGR2HSV);
				split(dst, channels);
				equalizeHist(channels[2], channels[2]);
				merge(channels, dst);
				cvtColor(dst, dst, COLOR_HSV2BGR);
			}
			if (gaussian == 1) GaussianBlur(dst, dst, Size(7, 7), 0, 0);
			if(num2 == 2) threshold(dst, dst, thresh_value, 255, THRESH_BINARY);
		}
		
		imshow("frame", frame);
		imshow("dst", dst);

		//여기서부터 저장할 때
		if (savesave == 1) {
			if(gray == 0) output.write(dst);
			else {
				cvtColor(dst, dst, COLOR_GRAY2RGB);
				output.write(dst);
			}
		}
	}
}
void ssave(int id) {
	save++;
	if (save % 2 == 1) {
		savesave = 1;
		output.open("output.avi", fourcc, fps, Size(width, height), true);
	}
	else { 
		savesave = 0; 
		output.release();
	}
}
void video_start(int id) {
	destroyAllWindows();
	namedWindow("file", 1);
	namedWindow("file_dst", 1);
	OpenFileDialog* openFileDialog = new OpenFileDialog();
	if (openFileDialog->ShowDialog()) {
		Filename = openFileDialog->FileName;
		if (!video.isOpened()) cout << "동영상을 열 수 없음\n";
		video.open(Filename);
	}
	start_num = 1;
}
void cam_start(int id) {
	destroyAllWindows();
	namedWindow("frame", 1);
	namedWindow("dst", 1);
	start_num = 2;
}
void spinner_resize(int id) {
	width = spin_W->get_int_val();
	height = spin_H->get_int_val();
}

int main(int argc, char* argv[])
{
	glutInit(&argc, argv);
	GLUI* glui = GLUI_Master.create_glui("Menu", 0);
	main_window = glui->get_glut_window_id();
	GLUI_Master.set_glutIdleFunc(open);

	GLUI_Panel* panel1 = glui->add_panel("", GLUI_PANEL_EMBOSSED);
	GLUI_RadioGroup* rg1 = glui->add_radiogroup_to_panel(panel1, &num, 1, rg1_option);
	GLUI_RadioButton* VideoFile = glui->add_radiobutton_to_group(rg1, "Video File");
	GLUI_RadioButton* Camera = glui->add_radiobutton_to_group(rg1, "Camera");
	//끄는거 만들어야함
	
	FileOpen = glui->add_button("File Open", 1, video_start);
	Start = glui->add_button("Start", 1, cam_start);
	Start->disable(); 


	GLUI_Panel* panel2 = glui->add_panel("", GLUI_PANEL_EMBOSSED);
	spin_W = glui->add_spinner_to_panel(panel2, "W", GLUI_SPINNER_INT, &width, 1, spinner_resize);
	spin_W->set_int_val(640);
	spin_W->set_int_limits(10, 800, GLUI_LIMIT_CLAMP);
	spin_W->set_speed(0.0001);
	
	spin_H = glui->add_spinner_to_panel(panel2, "H", GLUI_SPINNER_INT, &height, 1, spinner_resize);
	spin_H->set_int_val(480);
	spin_H->set_int_limits(10, 800, GLUI_LIMIT_CLAMP); 
	spin_H->set_speed(0.0001);
	
	GLUI_Checkbox* Save = glui->add_checkbox("Save", 0, 1, ssave);
	Save->set_alignment(GLUI_ALIGN_CENTER);

	GLUI_Panel* panel3 = glui->add_panel("", GLUI_PANEL_EMBOSSED);
	GLUI_Checkbox* Grayscale = glui->add_checkbox_to_panel(panel3, "To Grayscale", &gray, 1);
	
	glui->add_separator_to_panel(panel3);
	
	GLUI_Checkbox* Gaussian = glui->add_checkbox_to_panel(panel3, "Gaussian Low", &gaussian, 1);
	GLUI_Checkbox* Histogram = glui->add_checkbox_to_panel(panel3, "Histogram Eq", &histogram, 1);
	
	glui->add_separator_to_panel(panel3);
		
	GLUI_RadioGroup* rg2 = glui->add_radiogroup_to_panel(panel3, &num2, 1);
	GLUI_RadioButton* None = glui->add_radiobutton_to_group(rg2, "None");
	GLUI_RadioButton* Canny = glui->add_radiobutton_to_group(rg2, "Canny");
	GLUI_RadioButton* Threshold = glui->add_radiobutton_to_group(rg2, "Threshold");
	GLUI_Scrollbar* Th = new GLUI_Scrollbar(panel3, "", GLUI_SCROLL_HORIZONTAL, &thresh_value, 0);
	Th->set_int_limits(0, 255);

	GLUI_Button* Quit = glui->add_button("Quit", 0, (GLUI_Update_CB)exit);
	
	glui->set_main_gfx_window(main_window);
	glutMainLoop();
	return EXIT_SUCCESS;
}
