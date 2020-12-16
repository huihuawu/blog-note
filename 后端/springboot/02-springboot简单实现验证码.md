# Springboot验证码

## 简单实现验证码功能

> 编写对应的`Controller`

```java
@GetMapping(value = "/code")
@ResponseBody
public void getCode(HttpServletRequest request,
                    HttpServletResponse response){
    IVerifyCodeGen iVerifyCodeGen = new CharVerifyCodeGenImpl();
    try {
        //设置长宽
        VerifyCode verifyCode = iVerifyCodeGen.generate(80, 28);
        String code = verifyCode.getCode();
        // 实际场景肯定需要将生成的验证码的值保存起来，相应的处理
        //设置响应头
        response.setHeader("Pragma", "no-cache");
        //设置响应头
        response.setHeader("Cache-Control", "no-cache");
        //在代理服务器端防止缓冲
        response.setDateHeader("Expires", 0);
        //设置响应内容类型
        response.setContentType("image/jpeg");
        response.getOutputStream().write(verifyCode.getImgBytes());
        response.getOutputStream().flush();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

> 导入对应的工具类
>
> 新建`IVerifyCodeGen.java`验证码生成接口类：

```java
import java.io.IOException;
import java.io.OutputStream;

/********************************
 * Description: 验证码生成接口
 * @Author: codehui
 * @Date: 2020年12月16 14:51
 * @Version: 1.0
 ********************************/
public interface IVerifyCodeGen {
    /**
     * 生成验证码并返回code，将图片写的os中
     */
    String generate(int width, int height, OutputStream os) throws IOException;

    /**
     * 生成验证码对象
     */
    VerifyCode generate(int width, int height) throws IOException;
}
```

> 新建上面验证码生成接口类的具体实现类：`CharVerifyCodeGenImpl.java`

```java
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Random;
/********************************
 * Description:验证码实现类
 * @Author: codehui
 * @Date: 2020年12月16 14:57
 * @Version: 1.0
 ********************************/
public class CharVerifyCodeGenImpl implements IVerifyCodeGen {
    
    private static final String[] FONT_TYPES = { "\u5b8b\u4f53", "\u65b0\u5b8b\u4f53", "\u9ed1\u4f53", "\u6977\u4f53", "\u96b6\u4e66" };

    private static final int VALICATE_CODE_LENGTH = 4;

    /**
     * 设置背景颜色及大小，干扰线
     *
     * @param graphics
     * @param width
     * @param height
     */
    private static void fillBackground(Graphics graphics, int width, int height) {
        // 填充背景
        graphics.setColor(Color.WHITE);
        //设置矩形坐标x y 为0
        graphics.fillRect(0, 0, width, height);

        // 加入干扰线条
        for (int i = 0; i < 8; i++) {
            //设置随机颜色算法参数
            graphics.setColor(RandomUtils.randomColor(40, 150));
            Random random = new Random();
            int x = random.nextInt(width);
            int y = random.nextInt(height);
            int x1 = random.nextInt(width);
            int y1 = random.nextInt(height);
            graphics.drawLine(x, y, x1, y1);
        }
    }

    /**
     * 生成随机字符
     *
     * @param width
     * @param height
     * @param os
     * @return
     * @throws IOException
     */
    @Override
    public String generate(int width, int height, OutputStream os) throws IOException {
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
        Graphics graphics = image.getGraphics();
        fillBackground(graphics, width, height);
        String randomStr = RandomUtils.randomString(VALICATE_CODE_LENGTH);
        createCharacter(graphics, randomStr);
        graphics.dispose();
        //设置JPEG格式
        ImageIO.write(image, "JPEG", os);
        return randomStr;
    }

    /**
     * 验证码生成
     *
     * @param width
     * @param height
     * @return
     */
    @Override
    public VerifyCode generate(int width, int height) {
        VerifyCode verifyCode = null;
        try (
                //将流的初始化放到这里就不需要手动关闭流
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
        ) {
            String code = generate(width, height, baos);
            verifyCode = new VerifyCode();
            verifyCode.setCode(code);
            verifyCode.setImgBytes(baos.toByteArray());
        } catch (IOException e) {
            verifyCode = null;
        }
        return verifyCode;
    }

    /**
     * 设置字符颜色大小
     *
     * @param g
     * @param randomStr
     */
    private void createCharacter(Graphics g, String randomStr) {
        char[] charArray = randomStr.toCharArray();
        for (int i = 0; i < charArray.length; i++) {
            //设置RGB颜色算法参数
            g.setColor(new Color(50 + RandomUtils.nextInt(100),
                    50 + RandomUtils.nextInt(100), 50 + RandomUtils.nextInt(100)));
            //设置字体大小，类型
            g.setFont(new Font(FONT_TYPES[RandomUtils.nextInt(FONT_TYPES.length)], Font.BOLD, 26));
            //设置x y 坐标
            g.drawString(String.valueOf(charArray[i]), 15 * i + 5, 19 + RandomUtils.nextInt(8));
        }
    }
}
```



> 新建`VerifyCode.java`类

```java
/********************************
 * Description: 验证码类
 * @Author: codehui
 * @Date: 2020年12月16 14:53
 * @Version: 1.0
 ********************************/
public class VerifyCode {

    private String code;
    private byte[] imgBytes;
    private long expireTime;

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }

    public byte[] getImgBytes() {
        return imgBytes;
    }

    public void setImgBytes(byte[] imgBytes) {
        this.imgBytes = imgBytes;
    }

    public long getExpireTime() {
        return expireTime;
    }

    public void setExpireTime(long expireTime) {
        this.expireTime = expireTime;
    }
}
```

> 新建`RandomUtils.java`工具类

```java
import java.awt.*;
import java.util.Random;
/********************************
 * Description:
 * @Author: codehui
 * @Date: 2020年12月16 15:00
 * @Version: 1.0
 ********************************/
public class RandomUtils extends org.apache.commons.lang3.RandomUtils {
    
    /**
     * 使用到lang3包，需要导入依赖
     * <dependency>
     *     <groupId>org.apache.commons</groupId>
     *     <artifactId>commons-lang3</artifactId>
     * </dependency>
     */
    private static final char[] CODE_SEQ = { 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'J',
            'K', 'L', 'M', 'N', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W',
            'X', 'Y', 'Z', '2', '3', '4', '5', '6', '7', '8', '9' };

    private static final char[] NUMBER_ARRAY = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' };

    private static Random random = new Random();

    public static String randomString(int length) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < length; i++) {
            sb.append(String.valueOf(CODE_SEQ[random.nextInt(CODE_SEQ.length)]));
        }
        return sb.toString();
    }

    public static String randomNumberString(int length) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < length; i++) {
            sb.append(String.valueOf(NUMBER_ARRAY[random.nextInt(NUMBER_ARRAY.length)]));
        }
        return sb.toString();
    }

    public static Color randomColor(int fc, int bc) {
        int f = fc;
        int b = bc;
        Random random = new Random();
        if (f > 255) {
            f = 255;
        }
        if (b > 255) {
            b = 255;
        }
        return new Color(f + random.nextInt(b - f), f + random.nextInt(b - f), f + random.nextInt(b - f));
    }

    public static int nextInt(int bound) {
        return random.nextInt(bound);
    }
}
```

实现的效果：

![code](imgs/code.png)

前端使用：

```html
<div>
    <img src="http://localhost:8080/code" ref="verifyCodeImg" @click="changeVerifyCode" id="verifyCodeImg"/>
</div>

 /**
  * 这里前端使用的是vue
  * 更新验证码
  * 只需要将src改变即可
  */
  changeVerifyCode(){
    this.$refs.verifyCodeImg.src = "http://localhost:8080/code?code=" + Math.random()
    //后面的参数其实不起作用，目的是为了更改请求，不然不会发送请求
  },
```

