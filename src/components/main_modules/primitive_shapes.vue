<script>
    //Helper functions
    function fillRectangle(graphics){
        graphics.save();
        graphics.beginPath();
        graphics.rect(0,0,1,1);
        graphics.fill();
        graphics.restore();
    }
    function rectangle(graphics){
        graphics.save();
        graphics.beginPath();
        graphics.rect(0,0,1,1);
        graphics.stroke();
        graphics.restore();
    }
    function line(graphics){
        graphics.save()
        graphics.beginPath();
        graphics.moveTo(0,0);
        graphics.lineTo(0,1);
        graphics.stroke()
        graphics.restore()
    }
    function fillCircle(graphics){
        graphics.save()
        graphics.beginPath()
        graphics.arc(0,0,1,0,Math.PI*2)
        graphics.closePath()
        graphics.fill()
        graphics.restore()
    }
    function circle(graphics){
        graphics.save()
        graphics.beginPath()
        graphics.arc(0,0,1,0,Math.PI*2)
        graphics.closePath()
        graphics.stroke()
        graphics.restore()
    }

    function fillTriangle(graphics){
        graphics.save()
        graphics.beginPath()
        graphics.moveTo(-0.5,0)
        graphics.lineTo(0,1)
        graphics.lineTo(0.5,0)
        graphics.closePath()
        graphics.fill()
        graphics.restore()
    }

    function triangle(graphics){
        graphics.save()
        graphics.beginPath()
        graphics.moveTo(-0.5,0)
        graphics.lineTo(0,1)
        graphics.lineTo(0.5,0)
        graphics.closePath()
        graphics.stroke()
        graphics.restore()
    }
    export {rectangle,fillRectangle, triangle, fillTriangle, circle, fillCircle, line}
</script>