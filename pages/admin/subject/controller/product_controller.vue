<template>
  <div></div>
</template>
<script>
import Vue from 'vue'
export const CheckedEventBus = new Vue()
export default {

  data: () => ({

  }),

  created() {
    CheckedEventBus.$on('SaveProduct', (item, callback) => {
      this.check_sendMethod(item, callback)
    })
    CheckedEventBus.$on('SaveDetailProduct', (item, callback) => {
      this.save_detail_pro(item, callback)
    })
    CheckedEventBus.$on('Search_Product', (callback) => {
      this.search_Product(callback)
    })
  },

  methods: {
    check_sendMethod(item, callback) {
      console.log('ทำๆ', item)
      const db = this.$fireModule.database()
      db.ref(`product/`)
        .push({
          name: item.name,
        })
        .then((snapshot) => {
          db.ref(`product/${snapshot.key}`).update({ id: snapshot.key })
          const result = 'บันทึกสำเร็จ'
          callback(result)
        })
        .catch((error) => {
          const result = error
          callback(result)
        })

      // เรียก callback function แล้วส่งค่าที่คุณต้องการส่งกลับ
    },

    save_detail_pro(item, callback) {
      const db = this.$fireModule.database()
      db.ref(`detail_product/`).push({
            name: item.name,
            idClass: item.idClass
        }).then((snapshot) => {
          db.ref(`detail_product/${snapshot.key}`).update({ id: snapshot.key })
          const result = 'บันทึกสำเร็จ'
          callback(result)
        })
        .catch((error) => {
          const result = error
          callback(result)
        })
    },

    search_Product(callback) {
      const db = this.$fireModule.database()
      db.ref('product/').once('value', (snapshot) => {
        let item = []
        let num = 0
        const childData = snapshot.val()
        for (const key in childData) {
          const product = childData[key]
          num += 1
          item.push({ name: product.name, id: product.id, index: num })
          console.log('>>>',item);
        }
        const result = item;
        callback(result);
      })
    },
    
  },
}
</script>
