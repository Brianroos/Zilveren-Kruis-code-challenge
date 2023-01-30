<template>
    <div class="card card--filled-secondary h-auto">
        <div class="card-body">
            <div class="sf-contactblok-content">
                <h3 class="mb-2">We staan voor je klaar</h3>
                <p class="mb-5">Twijfel je? Heb je een vraag?</p>
                <div class="phonenumber mb-2">
                    <a href="tel:0900 040 03 09">
                        <span class="sr-only"> Telefoonnummer </span>
                        0900 040 03 09
                    </a>
                    <div class="popover__container ml-1">
                        <button
                            id="info-phone"
                            type="button"
                            class="btn popover__btn btn--popover btn-none"
                        >
                            <span class="sr-only"> Informatie weergeven </span>
                        </button>
                    </div>
                </div>
                <small> Open op maandag t/m vrijdag van 08:00 tot 17:00 </small>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'ZKContactBlock'
};
</script>
