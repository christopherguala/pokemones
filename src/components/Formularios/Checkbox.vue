<template>
  <section>
    <h3>3) Checkbox</h3>

    <!-- A) booleano -->
    <div class="block">
      <label class="label">A) Checkbox booleano</label>
      <label class="check">
        <input
          type="checkbox"
          :checked="aceptaTerminos"
          @change="$emit('update:aceptaTerminos', $event.target.checked)"
        />
        <span>Acepto términos y condiciones</span>
      </label>
      <p class="help">Estado: {{ aceptaTerminos }}</p>
    </div>

    <!-- B) múltiples -> array -->
    <div class="block">
      <label class="label">B) Checkboxes múltiples → array</label>

      <label class="check">
        <input
          type="checkbox"
          value="HTML"
          :checked="(skills || []).includes('HTML')"
          @change="onToggle('HTML', $event)"
        />
        <span>HTML</span>
      </label>

      <label class="check">
        <input
          type="checkbox"
          value="CSS"
          :checked="(skills || []).includes('CSS')"
          @change="onToggle('CSS', $event)"
        />
        <span>CSS</span>
      </label>

      <label class="check">
        <input
          type="checkbox"
          value="JavaScript"
          :checked="(skills || []).includes('JavaScript')"
          @change="onToggle('JavaScript', $event)"
        />
        <span>JavaScript</span>
      </label>

      <div class="box info mt">
        <strong>Seleccionadas:</strong> {{ (skills && skills.length) ? skills.join(', ') : '—' }}
      </div>
    </div>
  </section>
</template>

<script setup>
const props = defineProps(['aceptaTerminos', 'skills']);
const emit = defineEmits(['update:aceptaTerminos', 'update:skills']);

function onToggle(value, e) {
  const checked = e.target.checked;
  const set = new Set(props.skills || []);
  if (checked) set.add(value); else set.delete(value);
  emit('update:skills', Array.from(set));
}
</script>

