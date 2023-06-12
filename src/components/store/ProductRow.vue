<template>
  <tr>
    <td>{{ product.title }}</td>
    <td>{{ product.price }}</td>
    <td>{{ product.description }}</td>
  </tr>
</template>

<script>
export default {
  props: {
    product: {
      type: Object,
      required: true
    }
  }
}
</script>