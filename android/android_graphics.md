# Android Graphics

## Bitmap

```java
Bitmap bmImg = BitmapFactory.decodeFile("path of your img1");
imageView.setImageBitmap(bmImg);
```

### Bitmap Resizing

```java
    public static void scaleBitmap(InputStream is, OutputStream os, float newWidth, float newHeight) {
        final Bitmap src_bitmap = BitmapFactory.decodeStream(is);
        final Bitmap scaled_bitmap = scaleBitmap(src_bitmap, newWidth, newHeight);
        src_bitmap.recycle();

        scaled_bitmap.compress(Bitmap.CompressFormat.JPEG, 90, os);
        scaled_bitmap.recycle();
    }

    public static Bitmap scaleBitmap(Bitmap bitmapToScale, float newWidth, float newHeight) {
        if(bitmapToScale == null)
            return null;
        //get the original width and height
        int width = bitmapToScale.getWidth();
        int height = bitmapToScale.getHeight();
        // create a matrix for the manipulation
        Matrix matrix = new Matrix();

        // resize the bit map
        matrix.postScale(newWidth / width, newHeight / height);

        // recreate the new Bitmap and set it back
        return Bitmap.createBitmap(bitmapToScale, 0, 0, bitmapToScale.getWidth(), bitmapToScale.getHeight(), matrix, true);
    }
```

### Recycle
Bitmapオブジェクトを使う場合は、使い終わったらrecycleをすること。これをしないとメモリ上から解放されない。これはBitmapFactoryでdecodeする際の実装が、2.x系ではNativeHeap領域に画像メモリを確保するため、それを解放するための明示的なコールになる（3系からはJaveHeap領域に画像メモリを確保するようになった）。
```java
//インスタンス化
Bitmap bitmap = BitmapFactory.decodeXXXXX();

//解放
if(bitmap != null){
     bitmap.recycle();
     bitmap = null;
}

//もっかい使う
if(bitmap.isRecycled()){
     bitmap = BitmapFactory.decodeXXXXXX()
}
```

### 회전

```java
final Bitmap bmp = ((BitmapDrawable)imageView.getDrawable()).getBitmap();

Matrix matrix = new Matrix();
matrix.postRotate(90);

final Bitmap newBmp = Bitmap.createBitmap(bmp, 0, 0, bmp.getWidth(), bmp.getHeight(), matrix, true);

if (!bmp.isRecycled()) bmp.recycle();
imageView.setImageBitmap(newBmp);
```

## Drawable
### Resource로부터 Drawable 가져오기

```java
    public static Drawable getDrawableFromResource(Context context, int res_id) {
        Drawable drawable;

        if (android.os.Build.VERSION.SDK_INT < 21) {
            drawable = context.getResources().getDrawable(res_id);
        }
        else {
            drawable = context.getDrawable(res_id);
        }

        return drawable;
    }
```

### 복제

```java
Drawable clone = drawable.getConstantState().newDrawable();
```

**주의!!** <br/> 
같은 리소스ID로부터 만들어진 모든 Drawable 객체는 color filter 등의 상태를 공유하고 있다. 만약 복제의 목적이 이런 공유된 상태에서 분리시키려는 것이라면, clone을 하지말고 다음과 같은 코드를 사용할 것!

```java
// To make a drawable use a separate constant state
drawable.mutate();
```

### Grayscale

```java
    public static Drawable convertToGrayscale(Drawable drawable) {
        ColorMatrix matrix = new ColorMatrix();
        matrix.setSaturation(0);

        ColorMatrixColorFilter filter = new ColorMatrixColorFilter(matrix);
        drawable.setColorFilter(filter);

        return drawable;
    }
```

### 컬러 변환

```java
    public static Drawable changeColor(Drawable drawable, int color) {
        Drawable d = drawable.mutate();
        d.setColorFilter(color, PorterDuff.Mode.SRC_ATOP);

        return d;
    }

    public static Drawable changeColor_MultiplyMode(Drawable drawable, int color) {
        if (drawable == null) {
            return drawable;
        }

        Drawable d = drawable.mutate();
        d.clearColorFilter();
        d.setColorFilter(color, PorterDuff.Mode.MULTIPLY);
        return d;
    }
```

### Drawable 내용을 Canvas에 그리기

```java
            Canvas canvas = new Canvas(lq);
            Drawable drawable = drawImageView.getDrawable().mutate();
            drawable.setBounds(0, 0, canvas.getWidth(), canvas.getHeight());
            drawable.draw(canvas);
            canvas.save();
```

## 이미지 정보
### 이미지 파일로부터 이미지 크기 얻기(EXIF 정보도 포함)

```java
public static class Vector2i {
        public int x;
        public int y;

        public Vector2i() {}
        public Vector2i(int x, int y) {
            this.x = x;
            this.y = y;
        }

        public static Vector2i parse(String from) {
            if (from != null && !from.isEmpty()) {
                final Vector2i result = new Vector2i();

                final String[] parts = from.split(",");
                if (parts.length > 1) {
                    result.x = Integer.parseInt(parts[0]);
                    result.y = Integer.parseInt(parts[1]);

                    return result;
                }
            }

            return null;
        }
    }

    public static Vector2i getImageSize(String filepath, boolean applyOrientation) {
        final Vector2i result = new Vector2i();
        int orientation = 0;

        try {
            final ExifInterface exifInterface = new ExifInterface(filepath);
            orientation = exifInterface.getAttributeInt(ExifInterface.TAG_ORIENTATION, ExifInterface.ORIENTATION_UNDEFINED);
            result.x = exifInterface.getAttributeInt(ExifInterface.TAG_IMAGE_WIDTH, -1);
            result.y = exifInterface.getAttributeInt("ImageHeight", -1);

            if (result.x <= 0) {
                result.x = exifInterface.getAttributeInt("ExifImageWidth", -1);
            }
            if (result.y <= 0) {
                result.y = exifInterface.getAttributeInt("ExifImageHeight", -1);
            }
        }
        catch (IOException e) {
            e.printStackTrace();

            result.x = -1;
            result.y = -1;
        }

        if (result.x <= 0 || result.y <= 0) {
            final BitmapFactory.Options options = new BitmapFactory.Options();
            options.inJustDecodeBounds = true;

            BitmapFactory.decodeFile(filepath, options);
            result.x = options.outWidth;
            result.y = options.outHeight;
        }

        if (applyOrientation) switch (orientation) {
            case ExifInterface.ORIENTATION_ROTATE_90:
            case ExifInterface.ORIENTATION_ROTATE_270:
                final int backupX = result.x;
                result.x = result.y;
                result.y = backupX;
                break;
        }

        return result;
    }
```

### EXIF에 회전 정보 얻기

```java
    public static int getExifRotateDegree(String jepgFilePath) {
        int degree = 0;
        try {
            ExifInterface exifInterface = new ExifInterface(jepgFilePath);
            final int orientation = exifInterface.getAttributeInt(ExifInterface.TAG_ORIENTATION, ExifInterface.ORIENTATION_UNDEFINED);
            degree = exifOrientationToDegree(orientation);
        }
        catch (IOException e) {
            degree = 0;
            e.printStackTrace();
        }

        return degree;
    }

    static int exifOrientationToDegree(int orientation) {
        int degree = 0;

        if (orientation == ExifInterface.ORIENTATION_ROTATE_90) {
            degree = 90;
        } else if (orientation == ExifInterface.ORIENTATION_ROTATE_180) {
            degree = 180;
        } else if (orientation == ExifInterface.ORIENTATION_ROTATE_270) {
            degree = 270;
        }

        return degree;
    }
```

## Libraries
### Picasso

* http://square.github.io/picasso/

```java
Picasso.with(context)
    .load(url)
    .placeholder(R.drawable.user_placeholder)
    .error(R.drawable.user_placeholder_error)
    .into(imageView);

Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
Picasso.with(context).load("file:///android_asset/DvpvklR.png").into(imageView2);
Picasso.with(context).load(new File(...)).into(imageView3);
```

#### Gradle

```
compile 'com.squareup.picasso:picasso:2.5.2'
```
