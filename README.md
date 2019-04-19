# ndk_dlopen
a lightweight ndk utility that helps to bypass Android N's classloader-namespace restriction

# technology
OSR (On-Stack-Replacement)

# support
x86, x86_64, armeabi-v7a (thumb & arm), arm64-v8a


//*******************************************************
 void *  functionlib;
		FILE * pFile;
		int r =0.0f;
		int g =1.0f;
		int b =0.0f;
		int alpha =0.0f;
		void (*fn)(JNIEnv * env,  jclass jc,int r,int g,int b,int alpha) =NULL;
 
		createLog();
		wlog("1");
		functionlib = dlopen("libGlesso.so", RTLD_LAZY );
		wlog("2");
 
		if (functionlib)
		{
			fn = (void (*)(JNIEnv *,jclass, int,int, int, int)) dlsym(functionlib, "Java_com_glesso_NInterface_neuglCleanColor");
			wlog("3");
			if (fn)
			{
				wlog("4");
				fn(env,  jc, r, g, b, alpha);
			}
			else
			{
				wlog("error,do not find fun");
				fprintf(stderr, "Could not dlopen(\"libbar.so\"): %s\n", dlerror());
			}
			//*(void **)(&fn) =dlsym(functionlib, "glCleanColor");
 
			dlclose(functionlib);
		}
//*************************************************************************
需要将第三方库放到工程jni目录下新建目录libs下，再在libs下新建目录include将头文件包含进来具体.mk文件如下：
LOCAL_PATH := $(call my-dir)
 
include $(CLEAR_VARS)
LOCAL_MODULE    := Glesso
LOCAL_SRC_FILES := eslib/libGlesso.so
LOCAL_C_INCLUDES := eslib/include
include $(PREBUILT_SHARED_LIBRARY)
 
include $(CLEAR_VARS)
LOCAL_MODULE    := JNITestPro
LOCAL_SRC_FILES := JNITestPro.cpp
LOCAL_LDLIBS := -lGLESv2
LOCAL_LDLIBS += -llog -lz -lm
LOCAL_SHARED_LIBRARIES := Glesso
include $(BUILD_SHARED_LIBRARY)
在自己新建的用于生成头文件的java类中依次添加相应加载类库的方法: 
public class NativeInterface {
	
	public native static int init_view();
	public native static int draw_view();
	
	static{
		System.out.println("java load jnitestpro.so");
		System.loadLibrary("Glesso");                          
		System.loadLibrary("JNITestPro");         
		System.out.println("java loaded jnitestpro.so");
	}
}

至此JNI->JNI大致就能运作了。
by https://blog.csdn.net/helong275098760/article/details/20442113
