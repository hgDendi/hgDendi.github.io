# Attribute #

    <resource>
        <attr name="duration">
        </arrt>

        <declare-styleable name="TestView">
            <attr name="duration">
        </declare-styleable>

    </resources>


Java:
    public class TestView{
        public TestView(Context context,AttributeSet attrs){
            super(context,attrs);
            TypedArray typedArray = context.obtainStyledAttributes(Attrs,R.styleable.TestView);
            int duration = typedArray.getInt(R.styleable.TestView_duration,0);
            
        }
    }