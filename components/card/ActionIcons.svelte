<div
  class="mdc-card__action-icons {className}"
  {...exclude($$props, ['class'])}
><slot></slot></div>

<script>
  import {exclude} from '../exclude';

  let className = '';
  export {className as class};
</script>