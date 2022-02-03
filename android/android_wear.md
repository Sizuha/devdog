# Android Waer

https://gist.github.com/JaeWoongOh/f3390a9c82718050813e

## Debug
http://qiita.com/tnj/items/83c41872125f01588068

### Bluetooth でデバッグ接続する

Android Wear Device에서:
1. Watch를 켜고 설정 > 정보 > 빌드번호항목을 연타하게되면 설정 메뉴에 개발자 옵션이 생기게 됩니다.
1. 개발자 옵션으로 들어가 보면 블루투스 스누프로깅, ADB디버깅, 블루투에 대한 디버그 항목이 있는데 모두 사용으로 체크를 합니다. 

Android Device에 연결된 PC에서:
```
$ ./adb forward tcp:4444 localabstract:/adb-hub
$ ./adb connect localhost:4444
connected to localhost:4444
```
これで、 ホスト: 接続済み となり、あとは USB で繋いでいるのと同じように LogCat の表示が可能になります。ただし、 Bluetooth 接続なので、ファイルや画面の転送には結構時間が掛かります。あんまり気軽に使えるって感じではなさそうです。

## 배포 방법
아직까지 단독으로 Android Wear 앱을 배포할 수 있는 수단이 없다. 따라서, 일반 안드로이드 앱에 Wear 앱을 포함시켜서 함께 배포해야만 한다.

참고
* http://blog.naver.com/PostView.nhn?blogId=gh2501&logNo=220514927950&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1

\1. wearable 앱의 apk 파일을 mobile 앱의 res > raw 폴더에 복사

\2. mobile 앱의 res > xml > 폴더에 임의의 .xml 파일을 생성. (ex. wear_app.xml)

\3. wear_app.xml파일의 내용을 다음과 같이 편집.
```xml
<?xml version="1.0" encoding="utf-8"?>
<wearableApp package="해당 앱의 패키지명">
    <versionCode>1</versionCode>    <!-- 반드시 두 앱의 버전코드가 일치해야 함 -->
    <versionName>1.0</versionName>    <!-- 두 앱의 버전명이 일치해야 함 -->
    <rawPathResId>wear</rawPathResId>   <!-- wearable 앱 apk 파일명(이름 부분만) -->
</wearableApp>
</source>    
4. mobile 앱의 AndroidManifest.xml 파일의 application 태그에 다음과 같은 내용을 추가.
<source lang="xml">
        <meta-data
            android:name="com.google.android.wearable.beta.app"
            android:resource="@xml/wear_app" /> <!-- 앞서 생성한 xml -->
```

## Sample
### Watch
아날로그 시계 샘플.
```java5
/**
 * Analog watch face with a ticking second hand. In ambient mode, the second hand isn't shown. On
 * devices with low-bit ambient mode, the hands are drawn without anti-aliasing in ambient mode.
 */
public class MyWatchFace extends CanvasWatchFaceService {
    /**
     * Update rate in milliseconds for interactive mode. We update once a second to advance the
     * second hand.
     */
    private static final long INTERACTIVE_UPDATE_RATE_MS = TimeUnit.SECONDS.toMillis(1);

    private Bitmap mBackgroundImg;
    private Bitmap mNeedleHour, mNeedleMin, mNeedleSec;


    @Override
    public Engine onCreateEngine() {
        return new Engine();
    }

    private class Engine extends CanvasWatchFaceService.Engine {
        static final int MSG_UPDATE_TIME = 0;

        Paint mBackgroundPaint;
        boolean mAmbient;
        Time mTime;

        /**
         * Handler to update the time once a second in interactive mode.
         */
        final Handler mUpdateTimeHandler = new Handler() {
            @Override
            public void handleMessage(Message message) {
                switch (message.what) {
                    case MSG_UPDATE_TIME:
                        invalidate();
                        if (shouldTimerBeRunning()) {
                            long timeMs = System.currentTimeMillis();
                            long delayMs = INTERACTIVE_UPDATE_RATE_MS
                                    - (timeMs % INTERACTIVE_UPDATE_RATE_MS);
                            mUpdateTimeHandler.sendEmptyMessageDelayed(MSG_UPDATE_TIME, delayMs);
                        }
                        break;
                }
            }
        };

        final BroadcastReceiver mTimeZoneReceiver = new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                mTime.clear(intent.getStringExtra("time-zone"));
                mTime.setToNow();
            }
        };
        boolean mRegisteredTimeZoneReceiver = false;

        /**
         * Whether the display supports fewer bits for each color in ambient mode. When true, we
         * disable anti-aliasing in ambient mode.
         */
        boolean mLowBitAmbient;

        @Override
        public void onCreate(SurfaceHolder holder) {
            super.onCreate(holder);

            setWatchFaceStyle(new WatchFaceStyle.Builder(GirlsWatchFace.this)
                    .setCardPeekMode(WatchFaceStyle.PEEK_MODE_SHORT)
                    .setBackgroundVisibility(WatchFaceStyle.BACKGROUND_VISIBILITY_INTERRUPTIVE)
                    .setShowSystemUiTime(false)
                    .build());

            Resources resources = GirlsWatchFace.this.getResources();

            mBackgroundPaint = new Paint();
            mBackgroundPaint.setColor(resources.getColor(R.color.analog_background));

            mTime = new Time();
            
            mBackgroundImg = BitmapFactory.decodeResource(resources, R.drawable.clock_bg);
            mNeedleHour = BitmapFactory.decodeResource(resources, R.drawable.hour);
            mNeedleMin = BitmapFactory.decodeResource(resources, R.drawable.min);
            mNeedleSec = BitmapFactory.decodeResource(resources, R.drawable.second);

            mTextBoxImg = BitmapFactory.decodeResource(resources, R.drawable.fusen);
        }

        @Override
        public void onDestroy() {
            mUpdateTimeHandler.removeMessages(MSG_UPDATE_TIME);
            super.onDestroy();
        }

        @Override
        public void onPropertiesChanged(Bundle properties) {
            super.onPropertiesChanged(properties);
            mLowBitAmbient = properties.getBoolean(PROPERTY_LOW_BIT_AMBIENT, false);
        }

        @Override
        public void onTimeTick() {
            super.onTimeTick();
            invalidate();
        }

        @Override
        public void onAmbientModeChanged(boolean inAmbientMode) {
            super.onAmbientModeChanged(inAmbientMode);
            if (mAmbient != inAmbientMode) {
                mAmbient = inAmbientMode;
                if (mLowBitAmbient) {
                    mHandPaint.setAntiAlias(!inAmbientMode);
                }
                invalidate();
            }

            // Whether the timer should be running depends on whether we're visible (as well as
            // whether we're in ambient mode), so we may need to start or stop the timer.
            updateTimer();
        }

        void drawClockBackground(Canvas canvas, Rect bounds) {
            canvas.drawRect(0, 0, canvas.getWidth(), canvas.getHeight(), mBackgroundPaint);
            canvas.drawBitmap(mBackgroundImg, null, bounds, null);
        }

        float mScale = 1f;

        @Override
        public void onDraw(Canvas canvas, Rect bounds) {
            mTime.setToNow();

            int width = bounds.width();
            int height = bounds.height();

            mScale = (float)width / (float)mBackgroundImg.getWidth();

            drawClockBackground(canvas, bounds);

            // Find the center. Ignore the window insets so that, on round watches with a
            // "chin", the watch face is centered on the entire screen, not just the usable
            // portion.
            float centerX = width / 2f;
            float centerY = height / 2f;

            float secRot = mTime.second / 30f * (float) Math.PI;
            int minutes = mTime.minute;
            float minRot = minutes / 30f * (float) Math.PI;
            float hrRot = ((mTime.hour + (minutes / 60f)) / 6f) * (float) Math.PI;

            float secLength = centerX - 20;
            float minLength = centerX - 40;
            float hrLength = centerX - 80;

            if (!mAmbient) {
                drawNeedle_Sec(canvas, centerX, centerY, mTime.second);
            }

            drawNeedle_Min(canvas, centerX, centerY, minutes);
            drawNeedle_Hour(canvas, centerX, centerY, mTime.hour % 12, minutes);
        }

        void drawNeedle_Sec(Canvas canvas, float centerX, float centerY, float seconds) {
            canvas.save();
            canvas.rotate(360f / 60f * seconds + 180f, centerX, centerY);

            float x = centerX - mNeedleSec.getWidth()*mScale/2;
            float y = centerY - 82f*mScale;

            RectF destRect = new RectF(
                    x, y,
                    x + mNeedleSec.getWidth()*mScale,
                    y + mNeedleSec.getHeight()*mScale);

            canvas.drawBitmap(mNeedleSec, null, destRect, null);
            //canvas.drawBitmap(mNeedleSec, centerX - mNeedleSec.getWidth()/2f, centerY-82f, mHandPaint);
            canvas.restore();
        }

        void drawNeedle_Min(Canvas canvas, float centerX, float centerY, float minutes) {
            canvas.save();
            canvas.rotate(360f / 60f * minutes + 180f, centerX, centerY);

            float x = centerX - mNeedleMin.getWidth()*mScale/2;
            float y = centerY - 64f*mScale;

            RectF destRect = new RectF(
                    x, y,
                    x + mNeedleMin.getWidth()*mScale,
                    y + mNeedleMin.getHeight()*mScale);

            canvas.drawBitmap(mNeedleMin, null, destRect, null);
            //canvas.drawBitmap(mNeedleMin, centerX - mNeedleSec.getWidth()/2f, centerY-64f, mHandPaint);
            canvas.restore();
        }

        void drawNeedle_Hour(Canvas canvas, float centerX, float centerY, float hours, float minutes) {
            canvas.save();
            canvas.rotate(360f / 720f * (hours * 60f + minutes) + 180f, centerX, centerY);

            float x = centerX - mNeedleHour.getWidth()*mScale/2;
            float y = centerY - 94f*mScale;

            RectF destRect = new RectF(
                    x, y,
                    x + mNeedleHour.getWidth()*mScale,
                    y + mNeedleHour.getHeight()*mScale);

            canvas.drawBitmap(mNeedleHour, null, destRect, null);
            //canvas.drawBitmap(mNeedleHour, centerX - mNeedleSec.getWidth()/2f, centerY-94f, mHandPaint);
            canvas.restore();
        }

        @Override
        public void onVisibilityChanged(boolean visible) {
            super.onVisibilityChanged(visible);

            if (visible) {
                registerReceiver();

                // Update time zone in case it changed while we weren't visible.
                mTime.clear(TimeZone.getDefault().getID());
                mTime.setToNow();
            } else {
                unregisterReceiver();
            }

            // Whether the timer should be running depends on whether we're visible (as well as
            // whether we're in ambient mode), so we may need to start or stop the timer.
            updateTimer();
        }

        private void registerReceiver() {
            if (mRegisteredTimeZoneReceiver) {
                return;
            }
            mRegisteredTimeZoneReceiver = true;
            IntentFilter filter = new IntentFilter(Intent.ACTION_TIMEZONE_CHANGED);
            GirlsWatchFace.this.registerReceiver(mTimeZoneReceiver, filter);
        }

        private void unregisterReceiver() {
            if (!mRegisteredTimeZoneReceiver) {
                return;
            }
            mRegisteredTimeZoneReceiver = false;
            GirlsWatchFace.this.unregisterReceiver(mTimeZoneReceiver);
        }

        /**
         * Starts the {@link #mUpdateTimeHandler} timer if it should be running and isn't currently
         * or stops it if it shouldn't be running but currently is.
         */
        private void updateTimer() {
            mUpdateTimeHandler.removeMessages(MSG_UPDATE_TIME);
            if (shouldTimerBeRunning()) {
                mUpdateTimeHandler.sendEmptyMessage(MSG_UPDATE_TIME);
            }
        }

        /**
         * Returns whether the {@link #mUpdateTimeHandler} timer should be running. The timer should
         * only run when we're visible and in interactive mode.
         */
        private boolean shouldTimerBeRunning() {
            return isVisible() && !isInAmbientMode();
        }
    }
}
```
