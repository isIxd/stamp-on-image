<template>
  <div style="height: 100vh">
    <p>スタンプをクリックで画像に追加、ドラッグで移動</p>
    <div
      v-for="image in interactiveImages"
      :key="image.id"
      style="position: relative; display: inline-block; margin: 24px"
      :data-image-id="image.id"
    >
      <div
        :style="{
          backgroundImage: `url(${image.imageUrl})`,
          width: `${image.width}px`,
          height: `${image.height}px`,
        }"
      />
      <div
        v-for="stamp in image.stamps"
        :key="stamp.id"
        @mousedown="mousedown"
        :style="{
          backgroundImage: `url(${stamp.stampUrl})`,
          width: `${stamp.size}px`,
          height: `${stamp.size}px`,
          left: `${stamp.position.x * image.width}px`,
          top: `${stamp.position.y * image.height}px`,
        }"
        style="position: absolute; transform: translateX(-50%) translateY(-50%)"
        :data-stamp-id="stamp.id"
      />
      <div style="display: flex; justify-content: center; height: 100px">
        <div
          v-for="stamp in stamps"
          :key="stamp"
          :style="{
            'background-image': `url(${stamp})`,
          }"
          style="width: 50px; height: 50px; margin: 0 10px"
          @click="addStamp(image.id, stamp)"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      // スタンプ一覧
      stamps: [
        'https://joeschmoe.io/api/v1/stamp0',
        'https://joeschmoe.io/api/v1/stamp1',
        'https://joeschmoe.io/api/v1/stamp2',
      ],
      // スタンプを移動させているか
      isDragging: false,
      // 移動させているスタンプとそれに対応するimageのid
      draggingTarget: {
        image: 0,
        stamp: 0,
      },
      // image一覧
      interactiveImages: [
        {
          id: '0',
          imageUrl: 'https://picsum.photos/seed/image0/400/300',
          width: 400,
          height: 300,
          stamps: [
            {
              id: '0',
              stampUrl: 'https://joeschmoe.io/api/v1/stamp0',
              size: 50,
              position: {
                x: 0.5,
                y: 0.5,
              },
            },
          ],
        },
        {
          id: '1',
          imageUrl: 'https://picsum.photos/seed/image1/400/300',
          width: 400,
          height: 300,
          stamps: [],
        },
      ],
    }
  },
  methods: {
    mousedown(e) {
      // ドラッグ開始
      this.isDragging = true
      // ドラッグ対象をdraggingTargetに格納
      const stamp = e.srcElement
      const image = stamp.parentElement
      this.draggingTarget.image = image.dataset.imageId
      this.draggingTarget.stamp = stamp.dataset.stampId
    },
    // ドラッグ中にマウスに合わせてスタンプを移動させる
    mousemove(e) {
      // ドラッグ中ではなかったらreturn
      if (!this.isDragging) return
      // ドラッグ中のimageのエレメントを取得
      const targetElement = document.querySelector(
        `[data-image-id="${this.draggingTarget.image}"]`
      )
      // dataのinteractiveImagesから対象のimageを取得
      const targetImage = this.interactiveImages.find(
        (i) => i.id == this.draggingTarget.image
      )
      // imageから対象のstampを取得
      const targetStamp = targetImage.stamps.find(
        (i) => i.id == this.draggingTarget.stamp
      )
      // imageの座標などを取得
      const targetRect = targetElement.getBoundingClientRect()
      // マウス座標とimage座標から、stampの位置を書き換える
      targetStamp.position.x = (e.clientX - targetRect.left) / targetImage.width
      targetStamp.position.y = (e.clientY - targetRect.top) / targetImage.height
    },
    mouseup() {
      // ドラッグ終了
      this.isDragging = false
    },
    addStamp(imageId, stampUrl) {
      // imageIdからimageを取得
      const targetImage = this.interactiveImages.find((i) => i.id == imageId)
      // stampを追加
      targetImage.stamps.push({
        id: this.generateUniqueId(),
        stampUrl,
        size: 50,
        position: {
          x: 0.5,
          y: 0.5,
        },
      })
    },
    // 適当にUniqueなIdを作成する関数
    generateUniqueId() {
      return Math.round(Math.random() * 1000000000).toString(16)
    },
  },
  mounted() {
    document.body.addEventListener('mousemove', this.mousemove)
    document.body.addEventListener('mouseup', this.mouseup)
  },
}
</script>
