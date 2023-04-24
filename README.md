# Segment Anything for Stable Diffusion WebUI

This extension aim for connecting [AUTOMATIC1111 Stable Diffusion WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) and [Mikubill ControlNet Extension](https://github.com/Mikubill/sd-webui-controlnet) with [segment anything](https://github.com/facebookresearch/segment-anything/) and [GroundingDINO](https://github.com/IDEA-Research/GroundingDINO) to enhance Stable Diffusion/ControlNet inpainting, enhance ControlNet semantic segmentation, automate image matting and create LoRA/LyCORIS training set.

## News

- `2023/04/10`: [Release] SAM extension released! You can click on the image to generate segmentation masks.
- `2023/04/12`: [Feature] Mask expansion released by [@jordan-barrett-jm](https://github.com/jordan-barrett-jm)! You can expand masks to overcome edge problems of SAM.
- `2023/04/15`: [Feature] [GroundingDINO](https://github.com/IDEA-Research/GroundingDINO) support released! You can enter text prompts to generate bounding boxes and segmentation masks.
- `2023/04/15`: [Feature] API support released by [@jordan-barrett-jm](https://github.com/jordan-barrett-jm)!
- `2023/04/18`: [Feature] [ControlNet V1.1](https://github.com/lllyasviel/ControlNet-v1-1-nightly) inpainting support released! You can copy SAM generated masks to ControlNet to do inpainting. Note that you **must** update [ControlNet extension](https://github.com/Mikubill/sd-webui-controlnet) to use it. ControlNet inpainting has far better performance compared to general-purposed models, and you do not need to download inpainting-specific models anymore.
- `2023/04/24`: [Feature] Automatic segmentation support released! Functionalities with * require you to have [ControlNet extension](https://github.com/Mikubill/sd-webui-controlnet) installed. This update includes support for 
    - *[ControlNet V1.1](https://github.com/lllyasviel/ControlNet-v1-1-nightly) semantic segmentation
    - [Edit-Anything](https://github.com/sail-sg/EditAnything) un-semantic segmentation (Not tested)
    - Image layout generation (single image + batch process)
    - *Image masking with categories (single image + batch process)
    - *Inpaint not masked for ControlNet inpainting on txt2img panel

## FAQ

Thanks for suggestions from [github issues](https://github.com/continue-revolution/sd-webui-segment-anything/issues), [reddit](https://www.reddit.com/r/StableDiffusion/comments/12hkdy8/sd_webui_segment_everything/) and [bilibili](https://www.bilibili.com/video/BV1Tg4y1u73r/) to make this extension better.

There are already at least two great tutorials on how to use this extension. Check out [this video (Chinese)](https://www.bilibili.com/video/BV1Sm4y1B7Pg/) from [@ThisisGameAIResearch](https://github.com/ThisisGameAIResearch/) and [this video (Chinese)](https://www.bilibili.com/video/BV1Hh411j7b2/) from [@OedoSoldier](https://github.com/OedoSoldier). You can also check out my [demo](#demo).

You should know the following before submitting an issue.

1. This extension has moved into maintenance phase. I don't think there will be huge updates in the foreseeable future. Despite of this, I will continue to deal with issues, and monitor new research works to see if they are worth supporting. I welcome any community contribution and any feature requests.

2. You must use gradio>=3.23.0 and WebUI>=`22bcc7be` to use this extension. A1111 WebUI is stable, and some integrated package authors have also updated their packages (for example, if you are using the package from [@Akegarasu](https://github.com/Akegarasu), i.e. 秋叶整合包, it has already been updated according to [this video](https://www.bilibili.com/video/BV1iM4y1y7oA)). Also, supporting different versions of WebUI will be a huge time commitment, during which I can create many more features. Please update your WebUI and it is safe to use. I'm not planning to support some old commits of WebUI, such as `a9fed7c3`.

3. You are required to install [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) to use GroundingDINO. If your device does not have CUDA Toolkit installed, GroundingDINO will not find `_C`. Follow steps decribed [here](https://github.com/continue-revolution/sd-webui-segment-anything/issues/32#issuecomment-1513873296) to resolve the `_C` problem. DO NOT skip steps.

4. It is impossible to support [color inpainting](https://github.com/continue-revolution/sd-webui-segment-anything/issues/22) at this moment, because gradio wierdly enlarge the input image which slows down your browser, or even freeze your page. I have already implemented this feature, though, but I made it invisible. Note that ControlNet v1.1 inpainting model is very strong, and you do not need to rely on the traditional inpainting anymore. ControlNet v1.1 does not support color inpainting.

5. [Inpaint-Anything](https://github.com/geekyutao/Inpaint-Anything) and [Edit-Anything](https://github.com/sail-sg/EditAnything) and A LOT of other popular SAM extensions have been supported. For Inpaint-Anything, you may check [this issue](https://github.com/continue-revolution/sd-webui-segment-anything/issues/60) for how to use. For Edit-Anything, this extension has **in-theory** supported, but since they only published diffusers models which probably only work for SD 2.x + [diffusers package](https://github.com/huggingface/diffusers), I am unable to test at this moment. I will update once they release models in lllyasviel format. I am always open to support any other interesting applications, submit a feature request if you find another interesting one.

6. If you have a job opportunity and think I am a good fit, please feel free to send me an email.

7. If you want to sponsor me, please go to [sponsor](#sponsor) section and scan the corresponding QR code.

## Installment

Download this extension to `${sd-webui}/extensions` use whatever way you like (git clone or install from UI)

Choose one or more of the models below and put them to `${sd-webui}/models/sam`. **Do not change model name, otherwise this extension may fail due to a bug inside segment anything.**

Three types of SAM models are available. [vit_h](https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth) is 2.56GB, [vit_l](https://dl.fbaipublicfiles.com/segment_anything/sam_vit_l_0b3195.pth) is 1.25GB, [vit_b](https://dl.fbaipublicfiles.com/segment_anything/sam_vit_b_01ec64.pth) is 375MB. I myself tested vit_h on NVIDIA 3090 Ti which is good. If you encounter VRAM problem, you should switch to smaller models.

GroundingDINO packages, GroundingDINO models and ControlNet annotators will be automatically installed the first time you use them.

## GroundingDINO

GroundingDINO has been supported in this extension. It has the following functionalities:
1. You can use text prompt to automatically generate bounding boxes. You can separate different category names with `.`. SAM can convert these bounding boxes to masks
2. You can use point prompts with **ONE bounding box** to generate masks
3. You can go to `Batch Process` tab to do image matting and get LoRA/LyCORIS training set

However, there are some existing problems with GroundingDINO:
1. GroundingDINO will be install when you firstly use GroundingDINO features, instead of when you initiate the WebUI. Make sure that your terminal can have access to GitHub, otherwise you have to install GroundingDINO manually. GroundingDINO models will be automatically downloaded from [huggingFace](https://huggingface.co/ShilongLiu/GroundingDINO/tree/main). If your terminal cannot visit HuggingFace, please manually download the model and put it under `${sd-webui-sam}/models/grounding-dino`.
2. GroundingDINO requires your device to compile C++, which might take a long time and throw tons of exceptions. If you encounter `_C` problem, it's most probably because you did not install CUDA Toolkit. Follow steps decribed [here](https://github.com/continue-revolution/sd-webui-segment-anything/issues/32#issuecomment-1513873296). DO NOT skip steps. Otherwise, please go to [Grounded-SAM Issue Page](https://github.com/IDEA-Research/Grounded-Segment-Anything/issues) and submit an issue there. Despite of this, you can still use this extension for point prompts->segmentation masks even if you cannot install GroundingDINO, don't worry.
3. If you want to use point prompts, SAM can at most accept one bounding box. This extension will check if there are multiple bounding boxes. If multiple bounding boxes, this extension will disgard all point prompts; otherwise all point prompts will be effective. You may always select one bounding box you want.

For more detail, check [How to Use](#how-to-use) and [Demo](#demo).

## AutoSAM

Automatic Segmentation has been supported in this extension. It has the following functionalities:
1. You can use SAM to enhance semantic segmentation and copy the output to control_v11p_sd15_seg
2. You can generate random segmentation and copy the output to [EditAnything](https://github.com/sail-sg/EditAnything) ControlNet
3. You can generate image layout and edit them inside PhotoShop. Both single image and batch process are supported.
4. You can generate masks according to category IDs. This tend to be more accurate compared to purely SAM+GroundingDINO segmentation, if what you want is a large object.

However, there are some existing problems with AutoSAM:
1. You are required to install [Mikubill ControlNet Extension](https://github.com/Mikubill/sd-webui-controlnet) to use functionality 1 and 4. Please do not change the directory name (`sd-webui-controlnet`).
2. [EditAnything](https://github.com/sail-sg/EditAnything) only released SD2.1 diffusers models. Even if they release lllyasviel models, their models might not be compatible with most community-based SD1.5 models.
3. Image layout generation has a pretty bad performance for anime images. I discourage you from using this functionality if you are dealing with anime images. I'm not sure about the performance for real images.

## How to Use

### Single Image
- Upload your image
- Optionally add point prompts on the image. Left click for positive point prompt (black dot), right click for negative point prompt (red dot), left click any dot again to cancel the prompt. You must add point prompt if you do not wish to use GroundingDINO.
- Optionally check `Enable GroundingDINO`, select GroundingDINO model you want, write text prompt (separate different categories with `.`) and pick a box threshold (I highly recommend the default setting. High threshold may result in no bounding box). You must write text prompt if you do not wish to use point prompts.
- Optionally enable previewing GroundingDINO bounding box and click `Generate bounding box`. You must write text prompt to preview bounding box. After you see the boxes with number marked on the top-left corner, uncheck all the boxes you do not want. If you uncheck all boxes, you will have to add point prompts to generate masks.
- Click `Preview Segmentation` button. Due to the limitation of SAM, if there are multiple bounding boxes, your point prompts will not take effect when generating masks.
- Choose your favorite segmentation.
- Optionally check `Expand Mask` and specify the amount, then click `Update Mask`.

#### txt2img
- You may only copy image and mask to ControlNet inpainting. 
- Optionally check `ControlNet inpaint not masked` to invert mask colors and inpaint regions outside of the mask.
- Select the correct ControlNet index where you are using inpainting, if you wish to use Multi-ControlNet. 

#### img2img
- Update your ControlNet (**MUST**) and check `Allow other script to control this extension` on your ControlNet settings.
- Check `Copy to Inpaint Upload & ControlNet Inpainting`. There is no need to select ControlNet index.
- Configurate ControlNet panel. Click `Enable`, preprocessor choose `inpaint_global_harmonious`, model choose `control_v11p_sd15_inpaint [ebff9138]`. There is no need to upload image to the ControlNet inpainting panel.
- Click `Switch to Inpaint Upload` button. There is no need to upload another image or mask, just leave them blank. Write your prompts, configurate A1111 panel and click `Generate`.

### Batch Process
- Choose your SAM model, GroundingDINO model, text prompt, box threshold and mask expansion amount. Enter the source and destination directories of your images.
- Choose `Output per image` to configurate the number of masks per bounding box. I highly recommend 3, since some masks might be wierd.
- Click/unclick several checkboxes to configurate the images you want to save. See [demo](#demo) for what type of images these checkboxes represent.
- Click `Start batch process` and wait. If you see "Done" below this button, you are all set.

### Auto SAM

#### ControlNet

#### Image Layout

#### Mask by Category

## Demo
Point prompts demo

https://user-images.githubusercontent.com/63914308/230916163-af661008-5a50-496e-8b79-8be7f193f9e9.mp4

GroundingDINO demo

https://user-images.githubusercontent.com/63914308/232157480-757f6e70-673a-4023-b4ca-df074ed30436.mp4

Batch process image demo

![Configuration Image](https://user-images.githubusercontent.com/63914308/232157562-2f3cc9cc-310c-4b8b-89ba-216d2e014ca2.jpg) 

| Input Image | Output Image | Output Mask | Output Blend |
| --- | --- | --- | --- |
| ![Input Image](https://user-images.githubusercontent.com/63914308/232157678-fcaaf6b6-1805-49fd-91fa-8a722cc01c8a.png) | ![Output Image](https://user-images.githubusercontent.com/63914308/232157721-2754ccf2-b341-4b24-95f2-b75ac5b4fcd2.png) | ![Output Mask](https://user-images.githubusercontent.com/63914308/232157975-05de0b23-1225-4187-89b1-032c731b46eb.png) | ![Output Blend](https://user-images.githubusercontent.com/63914308/232158575-228f687c-8045-4079-bcf5-5a4dd0c8d7bd.png)

## API Support

### API Usage

We have added an API endpoint to allow for automated workflows.

The API utilizes both Segment Anything and GroundingDINO to return masks of all instances of whatever object is specified in the text prompt.

This is an extension of the existing [Stable Diffusion Web UI API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API).

There are 2 endpoints exposed
- GET `/sam-webui/heartbeat`
- POST `/sam-webui/image-mask`

The heartbeat endpoint can be used to ensure that the API is up.

The image-mask endpoint accepts a payload that includes your base64-encoded image.

Below is an example of how to interface with the API using requests.

### API Example

```
import base64
import requests
from PIL import Image
from io import BytesIO

url = "http://127.0.0.1:7860/sam-webui/image-mask"

def image_to_base64(img_path: str) -> str:
    with open(img_path, "rb") as img_file:
        img_base64 = base64.b64encode(img_file.read()).decode()
    return img_base64

payload = {
    "image": image_to_base64("IMAGE_FILE_PATH"),
    "prompt": "TEXT PROMPT",
    "box_threshold": 0.3,
    "padding": 30 #Optional param to pad masks
}
res = requests.post(url, json=payload)

for dct in res.json():
    image_data = base64.b64decode(dct['image'])
    image = Image.open(BytesIO(image_data))
    image.show()
```

## Contribute

Disclaimer: I have not thoroughly tested this extension, so there might be bugs. Bear with me while I'm fixing them :)

If you encounter a bug, please submit an issue. Please at least provide your WebUI version, your extension version, your browser version, errors on your browser console log if there is any, error on your terminal log if there is any, to save both of our time.

I welcome any contribution. Please submit a pull request if you want to contribute

## Star History

Give me a star if you like this extension!

[![Star History Chart](https://api.star-history.com/svg?repos=continue-revolution/sd-webui-segment-anything&type=Date)](https://star-history.com/#continue-revolution/sd-webui-segment-anything&Date)

## Sponsor

You can sponsor me via WeChat or Alipay.

| WeChat | Alipay |
| --- | --- |
| ![216aff0250c7fd2bb32eeb4f7aae623](https://user-images.githubusercontent.com/63914308/232824466-21051be9-76ce-4862-bb0d-a431c186fce1.jpg) | ![15fe95b4ada738acf3e44c1d45a1805](https://user-images.githubusercontent.com/63914308/232824545-fb108600-729d-4204-8bec-4fd5cc8a14ec.jpg) |
