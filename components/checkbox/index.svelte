<div
  bind:this={element}
  use:useActions={use}
  use:forwardEvents
  class="mdc-checkbox {className}"
  class:mdc-checkbox--disabled={disabled}
  {...exclude($$props, ['use', 'class', 'disabled', 'indeterminate', 'group', 'checked', 'value', 'inputProps'])}
>
  <input
    use:useActions={inputProps.use}
    class="mdc-checkbox__native-control {inputProps.class}"
    type="checkbox"
    {id}
    {disabled}
    bind:checked={nativeChecked}
    {value}
    on:change={handleChange}
    on:change on:input
    {...exclude(inputProps, ['use', 'class'])}
  />
  <div class="mdc-checkbox__background">
    <svg class="mdc-checkbox__checkmark" viewBox="0 0 24 24">
      <path class="mdc-checkbox__checkmark-path" fill="none" d="M1.73,12.91 8.1,19.28 22.79,4.59" />
    </svg>
    <div class="mdc-checkbox__mixedmark"></div>
  </div>
</div>

<script>
  import {MDCCheckbox} from '@material/checkbox';
  import {onMount, onDestroy, getContext} from 'svelte';
  import {current_component} from 'svelte/internal';
  import {forwardEventsBuilder} from '../forwardEvents';
  import {exclude} from '../exclude';
  import {useActions} from '../useActions';

  const forwardEvents = forwardEventsBuilder(current_component);
  let uninitializedValue = () => {};

  export let use = [];
  let className = '';
  export {className as class};
  export let disabled = false;
  export let indeterminate = false;
  export let group = uninitializedValue;
  export let checked = uninitializedValue;
  export let value = null;
  export let inputProps = {
    use: [],
    class: ''
  };

  let element;
  let checkbox;
  let formField = getContext('SMUI:formField');
  let id = getContext('SMUI:formField:id');
  let setChecked = getContext('SMUI:formField:setChecked');
  let nativeChecked = group === uninitializedValue ? (checked === uninitializedValue ? false : checked) : group.indexOf(value) !== -1;

  if (setChecked) {
    setChecked(nativeChecked);
  }

  $: if (checkbox) {
    if (group !== uninitializedValue) {
      const isChecked = group.indexOf(value) !== -1;
      if (checkbox.checked !== isChecked) {
        checkbox.checked = isChecked;
      }
    } else if (checkbox.checked !== checked) {
      checkbox.checked = checked;
    }
  }

  $: if (checkbox && checkbox.indeterminate !== indeterminate) {
    checkbox.indeterminate = indeterminate;
  }

  $: if (checkbox && checkbox.disabled !== disabled) {
    checkbox.disabled = disabled;
  }

  $: if (checkbox && checkbox.value !== value) {
    checkbox.value = value;
  }

  let oldChecked = checked;
  $: if (checked !== uninitializedValue) {
    if (checked === oldChecked) {
      checked = nativeChecked;
    } else if (nativeChecked !== checked) {
      nativeChecked = checked;
    }
    oldChecked = checked;
  }

  onMount(() => {
    checkbox = new MDCCheckbox(element);

    if (formField && formField()) {
      formField().input = radio;
    }
  });

  onDestroy(() => {
    checkbox.destroy();
  });

  function handleChange(e) {
    if (group !== uninitializedValue) {
      const idx = group.indexOf(value);
      if (e.target.checked && idx === -1) {
        group.push(value);
        group = group;
      } else if (!e.target.checked && idx !== -1) {
        group.splice(idx, 1);
        group = group;
      }
    }
  }

  export function getId() {
    return id;
  }
</script>

<style lang="scss" global>
  @import "@material/checkbox/mdc-checkbox";
</style>