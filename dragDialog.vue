<template>
    <el-dialog
        ref="dialogref"
        :append-to-body='true'
        :style="{top: '0vh'}"
        class="drag-dialog"
        :show-close="false"
        :fullscreen="isFullScreen"
        :close-on-click-modal="false"
        :visible.sync="visible">
        <div class="dialog-header" ref="dialogHeader" slot="title" @mousedown="down">
            <p @mousedown.stop="">{{title}}</p>
            <section class="icons" @mousedown.stop="">
                <span @click="updateDialogScope" :class="isFullScreen ? 'el-icon-crop' : 'el-icon-full-screen'"></span>
                <span @click="handleClose" class="el-icon-close"></span>
            </section>
        </div>
        <slot></slot>
        <template slot="footer"><slot name="footer"></slot></template>
    </el-dialog>
</template>

<script>
const template = {
    id: '',
    username: '',
    email: '',
    defaultRole: '',
    head: ''
}
export default {
    props: {
        title: {
            type: String,
            default: ""
        },
        visible: {
            type: Boolean,
            default: false
        },
        beforeClose: Function
    },
    watch: {
        isFullScreen(n) {
            if(n) {
                this.dialogRef.style.top = '0px';
                this.dialogRef.style.left = '0px';
                this.dialogRef.style.maxHeight = 'unset';
                this.dialogRef.style.transform = 'unset';
            } else {
                this.dialogRef.style.top = '60px';
                this.dialogRef.style.left = '50%';
                this.dialogRef.style.maxHeight = '80%';
                this.dialogRef.style.transform = 'translateX(-50%)';
            }
        }
    },
    computed: {
        dialogRef() {
            let dialog = this.$refs.dialogref.$refs.dialog;
            return dialog
        }
    },
    methods: {
        handleClose() {
            if (typeof this.beforeClose === 'function') {
                this.beforeClose();
                this.isFullScreen = false;
            } else {
                this.close();
            }
        },
        close() {this.$emit('close');this.$emit('update:visible', false);this.isFullScreen = false;},
        updateDialogScope() {
            this.isFullScreen = !this.isFullScreen;
            
        },
        down(e) {
            this.downPosition = {top: e.offsetY, left: e.offsetX};
            let dialog = this.$refs.dialogHeader;
            window.addEventListener("mousemove", this.move);
            window.addEventListener("mouseup", this.up);
        },
        move(e) {
            let top = e.clientY - this.downPosition.top, left = e.clientX - this.downPosition.left;
            if(top <= 0) {top = 0;}
            this.dialogRef.style.transform = 'unset';
            this.dialogRef.style.top = top + 'px'
            this.dialogRef.style.left = left + 'px'
        },
        up(e) {
            let dialog = this.$refs.dialogHeader;
            window.removeEventListener("mousemove", this.move);
            window.removeEventListener("mouseup", this.up);
        }
    },
    data() {
        return {
            isFullScreen: false,
            userInfoDialog: true,
            downPosition: {
                top: 0,
                left: 0
            }
        }
    }
}
</script>

<style lang="less" scoped>
@import "../common/css/resetDialog.css";
</style>
