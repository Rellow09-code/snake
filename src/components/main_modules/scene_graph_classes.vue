<script>
    //Scene graph classes
    class SceneNode{
        constructor(){
            this.fillStyle = null;
            this.strokeStyle = null;
        }
        doDraw(graphics){
            throw new Error("doDraw function not implemented");
        }
        draw(graphics){
            graphics.fillStyle = this.fillColor || graphics.fillStyle; 
            graphics.strokeStyle = this.strokeStyle || graphics.strokeStyle;
            this.doDraw(graphics)
        }
    }

    class CompoundObject extends SceneNode{
        constructor(){
            super();
            this.objects = []
        }
        add(node) {
            this.objects.push(node)
        }
        doDraw(graphics){
            this.objects.forEach(object => {
                object.draw(graphics)
            });
        }
        draw(graphics){
            graphics.save()
            this.doDraw(graphics)
            graphics.restore()
        }
    }

    class TransformedObject extends SceneNode{
        constructor(node){
            super();
            this.object = node
            this.scaleX = 1
            this.scaleY = 1
            this.translateX = 0
            this.translateY = 0
            this.rotation = 0
        }

        setScale(x,y){
            this.scaleX = x;
            this.scaleY = y;
            return this;
        }
        setTranslation(x,y){
            this.translateX = x
            this.translateY = y
            return this;
        }
        setRotation(theta){
            this.rotation = theta
            return this;
        }
        doDraw(graphics){
            graphics.strokeStyle = this.strokeStyle || graphics.strokeStyle
            graphics.fillStyle = this.fillStyle || graphics.fillStyle
            graphics.translate(this.translateX, this.translateY)
            graphics.rotate(this.rotation)
            graphics.scale(this.scaleX, this.scaleY)
            this.object.draw(graphics)
        }
        draw(graphics){
            graphics.save()
            this.doDraw(graphics)
            graphics.restore()
        }
    }
    export {SceneNode,CompoundObject,TransformedObject}
</script>