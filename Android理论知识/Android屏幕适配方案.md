# 1、概述

大家在Android开发时，肯定会觉得屏幕适配是个尤其痛苦的事，各种屏幕尺寸适配起来蛋疼无比。如果我们换个角度我们看下这个问题，不知道大家有没有了解过web前端开发，或者说大家对于网页都不陌生吧，其实适配的问题在web页面的设计中理论上也存在，为什么这么说呢？电脑的显示器的分辨率、包括手机分辨率，我敢说分辨率的种类远超过android设备的分辨率，那么有一个很奇怪的现象：

为什么Web页面设计人员从来没有说过，尼玛适配好麻烦？
那么，到底是什么原因，让网页的设计可以在千差万别的分辨率的分辨率中依旧能给用户一个优质的体验呢？带着这个疑惑，我问了下媳妇（前端人员），媳妇睁大眼睛问我：什么叫适配？fc，尼玛，看来的确没有这类问题。后来再我仔细的追问后，她告诉我，噢，这个尺寸呀，我都是设置为20%的。追根到底，其实就是一个原因，网页提供了百分比计算大小。

同样的，大家拿到UI给的设计图以后，是不是抱怨过尼玛你标识的都是px，我项目里面用dp，这什么玩意，和UI人员解释，UI妹妹也不理解。那么本例同样可以解决Android工程师和UI妹妹间的矛盾~UI给出一个固定尺寸的设计稿，然后你在编写布局的时候不用思考，无脑照抄上面标识的像素值，就能达到完美适配，理想丰不丰满~~。

然而，Android对于不同的屏幕给出的适配方案是dp，那么dp与百分比的差距到底在哪里？

# 2、dp vs 百分比

- dp

我们首先看下dp的定义：

> Density-independent pixel (dp)独立像素密度。标准是160dip.即1dp对应1个pixel，计算公式如：px = dp * (dpi / 160)，屏幕密度越大，1dp对应 的像素点越多。 
上面的公式中有个dpi，dpi为DPI是Dots Per Inch（每英寸所打印的点数），也就是当设备的dpi为160的时候1px=1dp；
好了，上述这些概念记不记得住没关系，只要记住一点dp是与像素无关的，在实际使用中1dp大约等于1/160inch。

那么dp究竟解决了适配上的什么问题？可以看出1dp = 1/160inch；那么它至少能解决一个问题，就是你在布局文件写某个View的宽和高为160dp*160dp，这个View在任何分辨率的屏幕中，显示的尺寸大小是大约是一致的（可能不精确），大概是 1 inch * 1 inch。

但是，这样并不能够解决所有的适配问题：

- 呈现效果仍旧会有差异，仅仅是相近而已

- 当设备的物理尺寸存在差异的时候，dp就显得无能为力了。为4.3寸屏幕准备的UI，运行在5.0寸的屏幕上，很可能在右侧和下侧存在大量的空白。而5.0寸的UI运行到4.3寸的设备上，很可能显示不下。
以上两点

一句话，总结下，dp能够让同一数值在不同的分辨率展示出大致相同的尺寸大小。但是当设备的尺寸差异较大的时候，就无能为力了。适配的问题还需要我们自己去做，于是我们可能会这么做：
```
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
    <!-- values-hdpi 480X800 -->  
    <dimen name="imagewidth">120dip</dimen>      
</resources>  
```
```
<resources>  
    <!-- values-hdpi-1280x800 -->  
    <dimen name="imagewidth">220dip</dimen>      
</resources>  
```
```
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
    <!-- values-hdpi  480X320 -->  
    <dimen name="imagewidth">80dip</dimen>      
</resources> 
```
> 上述代码片段来自网络，也就是说，我们为了优质的用户体验，依然需要去针对不同的dpi设置，编写多套数值文件。
可以看出，dp并没有能解决适配问题。下面看百分比。

- 百分比 
这个概念不用说了，web中支持控件的宽度可以去参考父控件的宽度去设置百分比，最外层控件的宽度参考屏幕尺寸设置百分比，那么其实中Android设备中，只需要支持控件能够参考屏幕的百分比去计算宽高就足够了。

比如，我现在以下几个需求：

- 对于图片展示的Banner，为了起到该有的效果，我希望在任何手机上显示的高度为屏幕高度的1/4
- 我的首页分上下两栏，我希望每个栏目的屏幕高度为11/24，中间间隔为1/12
- slidingmenu的宽度为屏幕宽度的80%

当然了这仅仅是从一个大的层面上来说，其实小范围布局，可能百分比将会更加有用。

那么现在不支持百分比，实现上述的需求，可能需要1、代码去动态计算（很多人直接pass了，太麻烦）;2、利用weight（weight必须依赖Linearlayout，而且并不能适用于任何场景）

再比如：我的某个浮动按钮的高度和宽度希望是屏幕高度的1/12，我的某个Button的宽度希望是屏幕宽度的1/3。

上述的所有的需求，利用dp是无法完成的，我们希望控件的尺寸可以按照下列方式编写：
```
   <Button
        android:text="@string/hello_world"
        android:layout_width="20%w"
        android:layout_height="10%h"/>
```
利用屏幕的宽和高的比例去定义View的宽和高。

好了，到此我们可以看到dp与百分比的区别，而百分比能够更好的解决我们的适配问题。

- some 适配tips

我们再来看看一些适配的tips

- 1.多用match_parent
- 2.多用weight
- 3.自定义view解决

其实上述3点tip，归根结底还是利用百分比，match_parent相当于100%参考父控件；weight即按比例分配；自定义view无非是因为里面多数尺寸是按照百分比计算的；

通过这些tips，我们更加的看出如果能在Android中引入百分比的机制，将能解决大多数的适配问题，下面我们就来看看如何能够让Android支持百分比的概念。

## 3、百分比的引入

### 1、引入

其实我们的解决方案，就是在项目中针对你所需要适配的手机屏幕的分辨率各自简历一个文件夹。

如下图：

<img src="http://img.blog.csdn.net/20150503174449732" width="300px"/>

然后我们根据一个基准，为基准的意思就是：

比如480*320的分辨率为基准
宽度为320，将任何分辨率的宽度分为320份，取值为x1-x320
高度为480，将任何分辨率的高度分为480份，取值为y1-y480
例如对于800*480的宽度480：

<img src="http://img.blog.csdn.net/20150503180400049" width="300px"/>

可以看到x1 = 480 / 基准 = 480 / 320 = 1.5 ;

其他分辨率类似~~ 
你可能会问，这么多文件，难道我们要手算，然后自己编写？不要怕，下文会说。

那么，你可能有个疑问，这么写有什么好处呢？

假设我现在需要在屏幕中心有个按钮，宽度和高度为我们屏幕宽度的1/2，我可以怎么编写布局文件呢？
```
<FrameLayout >

    <Button
        android:layout_gravity="center"
        android:gravity="center"
        android:text="@string/hello_world"
        android:layout_width="@dimen/x160"
        android:layout_height="@dimen/x160"/>

</FrameLayout>
```
可以看到我们的宽度和高度定义为x160，其实就是宽度的50%； 
那么效果图：

<img src="http://img.blog.csdn.net/20150503180542414" width="300px"/>

可以看到不论在什么分辨率的机型，我们的按钮的宽和高始终是屏幕宽度的一半。

- 对于设计图

假设现在的UI的设计图是按照480*320设计的，且上面的宽和高的标识都是px的值，你可以直接将px转化为x[1-320]，y[1-480]，这样写出的布局基本就可以全分辨率适配了。

你可能会问：设计师设计图的分辨率不固定怎么办？下文会说~

- 对于上文提出的几个dp做不到的

你可以通过在引入百分比后，自己试试~~

好了，有个最主要的问题，我们没有说，就是分辨率这么多，尼玛难道我们要自己计算，然后手写？

### 2、自动生成工具

好了，其实这样的文件夹手写也可以，按照你们需要支持的分辨率，然后编写一套，以后一直使用。

当然了，作为程序员的我们，怎么能做这么low的工作，肯定要程序来实现：

那么实现需要以下步骤：

- 分析需要的支持的分辨率
> 对于主流的分辨率我已经集成到了我们的程序中，当然对于特殊的，你可以通过参数指定。关于屏幕分辨率信息，可以通过该网站查询：http://screensiz.es/phone
- 编写自动生成文件的程序

代码如下
```
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.PrintWriter;


public class GenerateValueFiles {

    private int baseW;
    private int baseH;

    private String dirStr = "./res";

    private final static String WTemplate = "<dimen name=\"x{0}\">{1}px</dimen>\n";
    private final static String HTemplate = "<dimen name=\"y{0}\">{1}px</dimen>\n";

    /**
     * {0}-HEIGHT
     */
    private final static String VALUE_TEMPLATE = "values-{0}x{1}";

    private static final String SUPPORT_DIMESION = "320,480;480,800;480,854;540,960;600,1024;720,1184;720,1196;720,1280;768,1024;800,1280;1080,1812;1080,1920;1440,2560;";

    private String supportStr = SUPPORT_DIMESION;

    public GenerateValueFiles(int baseX, int baseY, String supportStr) {
        this.baseW = baseX;
        this.baseH = baseY;

        if (!this.supportStr.contains(baseX + "," + baseY)) {
            this.supportStr += baseX + "," + baseY + ";";
        }

        this.supportStr += validateInput(supportStr);

        System.out.println(supportStr);

        File dir = new File(dirStr);
        if (!dir.exists()) {
            dir.mkdir();

        }
        System.out.println(dir.getAbsoluteFile());

    }

    /**
     * @param supportStr
     *            w,h_...w,h;
     * @return
     */
    private String validateInput(String supportStr) {
        StringBuffer sb = new StringBuffer();
        String[] vals = supportStr.split("_");
        int w = -1;
        int h = -1;
        String[] wh;
        for (String val : vals) {
            try {
                if (val == null || val.trim().length() == 0)
                    continue;

                wh = val.split(",");
                w = Integer.parseInt(wh[0]);
                h = Integer.parseInt(wh[1]);
            } catch (Exception e) {
                System.out.println("skip invalidate params : w,h = " + val);
                continue;
            }
            sb.append(w + "," + h + ";");
        }

        return sb.toString();
    }

    public void generate() {
        String[] vals = supportStr.split(";");
        for (String val : vals) {
            String[] wh = val.split(",");
            generateXmlFile(Integer.parseInt(wh[0]), Integer.parseInt(wh[1]));
        }

    }

    private void generateXmlFile(int w, int h) {

        StringBuffer sbForWidth = new StringBuffer();
        sbForWidth.append("<?xml version=\"1.0\" encoding=\"utf-8\"?>\n");
        sbForWidth.append("<resources>");
        float cellw = w * 1.0f / baseW;

        System.out.println("width : " + w + "," + baseW + "," + cellw);
        for (int i = 1; i < baseW; i++) {
            sbForWidth.append(WTemplate.replace("{0}", i + "").replace("{1}",
                    change(cellw * i) + ""));
        }
        sbForWidth.append(WTemplate.replace("{0}", baseW + "").replace("{1}",
                w + ""));
        sbForWidth.append("</resources>");

        StringBuffer sbForHeight = new StringBuffer();
        sbForHeight.append("<?xml version=\"1.0\" encoding=\"utf-8\"?>\n");
        sbForHeight.append("<resources>");
        float cellh = h *1.0f/ baseH;
        System.out.println("height : "+ h + "," + baseH + "," + cellh);
        for (int i = 1; i < baseH; i++) {
            sbForHeight.append(HTemplate.replace("{0}", i + "").replace("{1}",
                    change(cellh * i) + ""));
        }
        sbForHeight.append(HTemplate.replace("{0}", baseH + "").replace("{1}",
                h + ""));
        sbForHeight.append("</resources>");

        File fileDir = new File(dirStr + File.separator
                + VALUE_TEMPLATE.replace("{0}", h + "")//
                        .replace("{1}", w + ""));
        fileDir.mkdir();

        File layxFile = new File(fileDir.getAbsolutePath(), "lay_x.xml");
        File layyFile = new File(fileDir.getAbsolutePath(), "lay_y.xml");
        try {
            PrintWriter pw = new PrintWriter(new FileOutputStream(layxFile));
            pw.print(sbForWidth.toString());
            pw.close();
            pw = new PrintWriter(new FileOutputStream(layyFile));
            pw.print(sbForHeight.toString());
            pw.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static float change(float a) {
        int temp = (int) (a * 100);
        return temp / 100f;
    }

    public static void main(String[] args) {
        int baseW = 320;
        int baseH = 400;
        String addition = "";
        try {
            if (args.length >= 3) {
                baseW = Integer.parseInt(args[0]);
                baseH = Integer.parseInt(args[1]);
                addition = args[2];
            } else if (args.length >= 2) {
                baseW = Integer.parseInt(args[0]);
                baseH = Integer.parseInt(args[1]);
            } else if (args.length >= 1) {
                addition = args[0];
            }
        } catch (NumberFormatException e) {

            System.err
                    .println("right input params : java -jar xxx.jar width height w,h_w,h_..._w,h;");
            e.printStackTrace();
            System.exit(-1);
        }

        new GenerateValueFiles(baseW, baseH, addition).generate();
    }

}
```
默认基准为480*320，当然对于特殊需求，通过命令行指定即可：

例如：基准 1280 * 800 ，额外支持尺寸：1152 * 735；4500 * 3200；


按照

> Java -jar xx.jar width height width,height_width,height
上述格式即可。

到此，我们通过编写一个工具，根据某基准尺寸，生成所有需要适配分辨率的values文件，做到了编写布局文件时，可以参考屏幕的分辨率；在UI给出的设计图，可以快速的按照其标识的px单位进行编写布局。基本解决了适配的问题。

> 本方案思想已经有公司投入使用，个人认为还是很不错的，如果大家有更好的方案来解决屏幕适配的问题，欢迎留言探讨或者直接贴出好文链接，大家可以将自己的经验进行分享，这样才能壮大我们的队伍~~。

Google已经添加了百分比支持库，详情请看：Android 百分比布局库(percent-support-lib) 解析与扩展
