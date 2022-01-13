<script lang="ts">
import Taro from "@tarojs/taro"
import { defineComponent, onMounted, PropType, ref, h } from "vue"
import { Image, DrawConfig } from "./types"
import { drawImage, drawText, drawBlock, drawLine } from "./utils/draw"
import {
  toPx,
  toRpx,
  getRandomId,
  getImageInfo,
  getLinearColor,
} from "./utils/tools"

export default defineComponent({
  name: "PosterBuilder",
  props: {
    showLoading: {
      type: Boolean,
      default: false,
    },
    config: {
      type: Object as PropType<DrawConfig>,
      default: () => ({}),
    },
  },
  emits: ["success", "fail"],
  setup(props, context) {
    const count = ref(1)
    const {
      width,
      height,
      backgroundColor,
      texts = [],
      blocks = [],
      lines = [],
      debug = false,
    } = props.config || {}

    const canvasId = getRandomId()

    /**
     * step1: 初始化图片资源
     * @param  {Array} images = imgTask
     * @return {Promise} downloadImagePromise
     */
    const initImages = (images: Image[]) => {
      const imagesTemp = images.filter((item) => item.url)
      const drawList = imagesTemp.map((item, index) =>
        getImageInfo(item, index)
      )
      return Promise.all(drawList)
    }

    /**
     * step2: 初始化 canvas && 获取其 dom 节点和实例
     * @return {Promise} resolve 里返回其 dom 和实例
     */
    const initCanvas = () =>
      new Promise<any>((resolve) => {
        setTimeout(() => {
          const pageInstance = Taro.getCurrentInstance()?.page || {} // 拿到当前页面实例
          const query = Taro.createSelectorQuery().in(pageInstance) // 确定在当前页面内匹配子元素
          query
            .select(`#${canvasId}`)
            .fields({ node: true, size: true, context: true }, (res) => {
              const canvas = res.node
              const ctx = canvas.getContext("2d")
              resolve({ ctx, canvas })
            })
            .exec()
        }, 300)
      })

    /**
     * @description 保存绘制的图片
     * @param  { object } config
     */
    const getTempFile = (canvas) => {
      Taro.canvasToTempFilePath(
        {
          canvas,
          success: (result) => {
            Taro.hideLoading()
            context.emit("success", result)
          },
          fail: (error) => {
            const { errMsg } = error
            if (errMsg === "canvasToTempFilePath:fail:create bitmap failed") {
              count.value += 1
              if (count.value <= 3) {
                getTempFile(canvas)
              } else {
                Taro.hideLoading()
                Taro.showToast({
                  icon: "none",
                  title: errMsg || "绘制海报失败",
                })
                context.emit("fail", errMsg)
              }
            }
          },
        },
        context
      )
    }

    /**
     * step2: 开始绘制任务
     * @param  { Array } drawTasks 待绘制任务
     */
    const startDrawing = async (drawTasks) => {
      // TODO: check
      // const configHeight = getHeight(config)
      const { ctx, canvas } = await initCanvas()

      canvas.width = width
      canvas.height = height

      // 设置画布底色
      if (backgroundColor) {
        ctx.save() // 保存绘图上下文
        const grd = getLinearColor(ctx, backgroundColor, 0, 0, width, height)
        ctx.fillStyle = grd // 设置填充颜色
        ctx.fillRect(0, 0, width, height) // 填充一个矩形
        ctx.restore() // 恢复之前保存的绘图上下文
      }
      // 将要画的方块、文字、线条放进队列数组
      const queue = drawTasks
        .concat(
          texts.map((item) => {
            item.type = "text"
            item.zIndex = item.zIndex || 0
            return item
          })
        )
        .concat(
          blocks.map((item) => {
            item.type = "block"
            item.zIndex = item.zIndex || 0
            return item
          })
        )
        .concat(
          lines.map((item) => {
            item.type = "line"
            item.zIndex = item.zIndex || 0
            return item
          })
        )

      queue.sort((a, b) => a.zIndex - b.zIndex) // 按照层叠顺序由低至高排序, 先画低的，再画高的
      for (let i = 0; i < queue.length; i++) {
        const drawOptions = {
          canvas,
          ctx,
          toPx,
          toRpx,
        }
        if (queue[i].type === "image") {
          await drawImage(queue[i], drawOptions)
        } else if (queue[i].type === "text") {
          drawText(queue[i], drawOptions)
        } else if (queue[i].type === "block") {
          drawBlock(queue[i], drawOptions)
        } else if (queue[i].type === "line") {
          drawLine(queue[i], drawOptions)
        }
      }

      setTimeout(() => {
        getTempFile(canvas) // 需要做延时才能能正常加载图片
      }, 300)
    }

    // start: 初始化 canvas 实例 && 下载图片资源
    const init = () => {
      if (props.showLoading)
        Taro.showLoading({ mask: true, title: "生成中..." })
      if (props.config?.images?.length) {
        initImages(props.config.images)
          .then((result) => {
            // 1. 下载图片资源
            startDrawing(result)
          })
          .catch((err) => {
            Taro.hideLoading()
            Taro.showToast({
              icon: "none",
              title: err.errMsg || "下载图片失败",
            })
            context.emit("fail", err)
          })
      } else {
        startDrawing([])
      }
    }

    onMounted(() => {
      init()
    })

    return () =>
      h("canvas", {
        type: "2d",
        id: canvasId,
        style: {
          position: "absolute",
          height: `${height}rpx`,
          width: `${width}rpx`,
          transform: `translate3d(${debug ? 0 : "-9999rpx"}, 0, 0)`,
        },
      })
  },
})
</script>
