let resizedImage = {
    data() {
        return {
            resizedSrc: [],
            crop: false,
            decodeFile: null
        }
    },
    methods: {
        resize(file, resizeWidth, resizeHeight) {
            let currentImage = URL.createObjectURL(file);
            const img = new Image();
            const width = resizeWidth ? resizeWidth : 400;
            const height = resizeHeight ? resizeHeight : 400;
            img.setAttribute("crossorigin", "anonymous"); // works for me
            img.src = currentImage;
            img.onload = () => {
                const scale = Math.max(
                    resizeWidth / img.width,
                    resizeHeight / img.height
                );
                const elem = document.createElement("canvas");
                elem.width = width;
                elem.height = height;
                const ctx = elem.getContext("2d");
                if (this.crop) {
                    const imageWidth = img.width * scale;
                    const imageHeight = img.height * scale;
                    const dx = (width - imageWidth) / 2;
                    const dy = (height - imageHeight) / 2;
                    ctx.drawImage(
                        img,
                        0,
                        0,
                        img.width,
                        img.height,
                        dx,
                        dy,
                        imageWidth,
                        imageHeight
                    );
                } else {
                    ctx.drawImage(img, 0, 0, width, height);
                }
                const dataurl = elem.toDataURL(file.type);
                this.resizedSrc.push(dataurl);
                let fileName = resizeWidth + "*" + resizeHeight + file.name;
                this.decodeFile = this.base64ToFile(dataurl, fileName);
            };

        },

        base64ToFile(binaryImage, filename) {
            var arr = binaryImage.split(","),
                mime = arr[0].match(/:(.*?);/)[1],
                bstr = atob(arr[1]),
                n = bstr.length,
                u8arr = new Uint8Array(n);

            while (n--) {
                u8arr[n] = bstr.charCodeAt(n);
            }
            let newDecodeFile = new File([u8arr], filename, { type: mime });
            this.uploadImage(newDecodeFile);
            return newDecodeFile;
        },
    },

};
export { resizedImage };
