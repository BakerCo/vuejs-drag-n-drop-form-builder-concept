<script>
export default {
  name: 'FormItem',
  props: {
    el: {
      required: true,
      type: String
    },
    type: {},
    name: {},
    placeholder: {},
    label: {
      type: String,
      default: null
    }
  },
  data() {
    return {
      fields: []
    };
  },
  methods: {
    createLabel(createElement) {
      return  createElement('label', {attrs: {for: this.name},class: 'non-interactive'}, this.label);
    },
    createInput(createElement) {
      return createElement(
          this.el,
          {
            attrs: {
              class: 'form-control non-interactive',
              name: this.name,
              id: this.name,
              type: this.type,
              placeholder: this.placeholder,
            }
          }
        );
    },
    createSelect(createElement) {
      return 'select';
    }
  },
  render(createElement) {
    return createElement(
      'div',
      {
        attrs: {
          'class': 'form-group',
        }
      },
      [
        this.createLabel(createElement),
        this.createInput(createElement),
      ]
    )
  }
}
</script>

<style scoped>
.non-interactive {
  pointer-events: none;
}
</style>
