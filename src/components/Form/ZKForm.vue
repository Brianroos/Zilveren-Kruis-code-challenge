<template>
    <h1>Aanmelden</h1>
    <h2>Gegevens</h2>
    <div class="form-group">
        <h3>Reden van aanmelding</h3>
        <div class="form-input my-4">
            <label id="aanmeldreden-label" class="input__title">
                Wat is de reden van uw aanvraag?
            </label>
            <div class="input__group">
                <select class="form-control" v-model="selectedReason">
                    <option
                        v-for="reason in reasons"
                        v-bind:key="reason"
                        v-bind:value="reason.value"
                    >
                        {{ reason.value }}
                    </option>
                </select>
            </div>
        </div>
    </div>
    <div class="form-group">
        <h3>Persoonlijke gegevens</h3>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Naam</label>
                <input
                    class="input__field form-control"
                    type="text"
                    v-model="firstName"
                />
            </div>
        </div>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Tussenvoegsels</label>
                <input
                    class="input__field form-control"
                    type="text"
                    v-model="prefixes"
                />
            </div>
        </div>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Achternaam</label>
                <input
                    class="input__field form-control"
                    type="text"
                    v-model="lastName"
                />
            </div>
        </div>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Geslacht</label>
                <div class="form-row">
                    <div
                        class="radio custom-radio radio__option"
                        v-for="gender in genders"
                        v-bind:key="gender"
                    >
                        <input
                            :id="stringToLowerCase(gender.value)"
                            class="radio__input custom-control-input"
                            type="radio"
                            name="geslacht"
                            v-model="selectedGender"
                            v-bind:value="gender.value"
                        />
                        <label
                            class="radio__label custom-control-label"
                            :for="stringToLowerCase(gender.value)"
                        >
                            {{ gender.value }}
                        </label>
                    </div>
                </div>
            </div>
        </div>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Geboortedatum</label>
                <ZKDatepicker v-model="dateOfBirth" />
            </div>
        </div>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Burgerservicenummer</label>
                <input
                    class="input__field form-control"
                    :class="IsInputInvalid"
                    type="text"
                    v-model="citizenServiceNumber"
                    @blur="onInputBlur"
                />
            </div>
            <div
                v-if="isCitizenServiceNumberValid === false"
                class="input__feedback invalid-feedback mt-1"
                aria-live="polite"
            >
                <span>
                    Helaas is het ingevoerde burgerservicenummer niet geldig.
                    Probeer het opnieuw.
                </span>
            </div>
        </div>
    </div>
    <h2 class="mt-5">Verzekering</h2>
    <div class="form-group">
        <h3>Basisverzekering</h3>
        <p>
            In Nederland is de basisverzekering verplicht. Iedereen wordt voor
            de basisverzekering geaccepteerd. De overheid bepaalt welke zorg
            hierin zit en dit is dus bij elke verzekeraar hetzelfde.
        </p>
        <div class="input__group">
            <label class="input__title">Kies uw basisverzekering</label>
            <div class="form-row">
                <div
                    class="radio__tile"
                    v-for="insurance in insurances"
                    v-bind:key="insurance"
                    v-bind:class="insurance.suggested && 'radio__tile--choice'"
                >
                    <div
                        v-if="insurance.suggested"
                        class="badge radio__tile-badge"
                    >
                        Meest gekozen
                    </div>
                    <div class="radio custom-radio radio__option">
                        <input
                            type="radio"
                            name="radio-insurance"
                            :id="
                                'radio-insurance-' +
                                stringToLowerCase(insurance.value)
                            "
                            class="radio__input custom-control-input"
                            v-model="controle.selectedInsurance"
                            v-bind:value="{
                                value: insurance.value,
                                price: setInsuranceBase(insurance),
                                price_per_year: insurance.price_per_year
                            }"
                        />
                        <label
                            :for="
                                'radio-insurance-' +
                                stringToLowerCase(insurance.value)
                            "
                            class="radio__label custom-control-label"
                        >
                            <p class="radio__description">
                                {{ insurance.value }}
                            </p>
                            <p class="radio__price">
                                {{ setInsuranceBase(insurance) }}
                            </p>
                        </label>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="form-group">
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">Kies je betaaltermijn</label>
                <select
                    class="form-control"
                    v-model="controle.selectedPaymentTerm"
                >
                    <option
                        v-for="paymentTerm in paymentTerms"
                        v-bind:key="paymentTerm"
                        v-bind:value="paymentTerm.value"
                    >
                        {{ paymentTerm.value }}
                    </option>
                </select>
            </div>
        </div>
    </div>
    <div class="form-group">
        <h3>Eigen risico</h3>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">
                    Kies de hoogste van het eigen risico
                </label>
                <select
                    class="form-control"
                    v-model="controle.selectedOwnRisk"
                    :disabled="!controle.selectedInsurance"
                >
                    <option
                        v-for="ownRisk in OwnRiskOptions"
                        v-bind:key="ownRisk"
                        v-bind:value="ownRisk.value"
                    >
                        {{ ownRisk.value }}
                    </option>
                </select>
            </div>
        </div>
    </div>
    <div class="form-group">
        <h3>Aanvullende verzekering</h3>
        <p>
            Onze aanvullende verzekeringen kennen ruim 100 verschillende
            vergoedingen. De hoogte van de vergoeding verschilt per pakket.
        </p>
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">
                    Kies uw aanvullende verzekering
                </label>
                <select
                    class="form-control"
                    v-model="controle.selectedAdditionalInsurance"
                >
                    <option
                        v-for="insurance in AdditionalInsurances"
                        v-bind:key="insurance"
                        v-bind:value="insurance.value"
                    >
                        {{ insurance.value }}
                    </option>
                </select>
            </div>
        </div>
    </div>
    <div class="form-group">
        <div class="form-input my-4">
            <div class="input__group">
                <label class="input__title">
                    Kies uw tandartsverzekering
                </label>
                <select
                    class="form-control"
                    v-model="controle.selectedDentalInsurance"
                >
                    <option
                        v-for="insurance in DentalInsurances"
                        v-bind:key="insurance"
                        v-bind:value="insurance.value"
                    >
                        {{ insurance.value }}
                    </option>
                </select>
            </div>
        </div>
    </div>

    <h2 class="mt-5">Controle</h2>
    <div class="form-group">
        <h3>Gekozen pakket</h3>
        <h3 class="mb-5" v-if="FullName">
            {{ FullName }}
            <span v-if="dateOfBirth">({{ FormattedDateOfBirth }})</span>
        </h3>
        <div v-if="controle.selectedInsurance">
            <h4>Basisverzekering</h4>
            <div class="form-group__selection">
                <div class="flex">
                    <p>{{ controle.selectedInsurance.value }}</p>
                    <p>
                        {{ numberToPrice(FinalBaseSum) }}
                        {{ this.controle.selectedPaymentTerm }}
                    </p>
                </div>
            </div>
        </div>
        <div class="form-group__selection" v-if="controle.selectedOwnRisk">
            <h4>Eigen risico</h4>
            <div>
                <p>{{ controle.selectedOwnRisk }}</p>
            </div>
        </div>
        <div class="form-group__selection">
            <h4>Aanvullende verzekering</h4>
            <div>
                <p>{{ controle.selectedAdditionalInsurance }}</p>
            </div>
        </div>
        <div class="form-group__selection">
            <h4>Tandartsverzekering</h4>
            <div>
                <p>{{ controle.selectedDentalInsurance }}</p>
            </div>
        </div>
    </div>
    <div class="card card--filled-secondary h-auto">
        <div class="card-body">
            <div class="sf-contactblok-content">
                <p class="mt-2 mb-2">
                    <b>{{ TotalDescription }}</b>
                </p>
                <h3 class="mb-2">{{ numberToPrice(FinalSum) }}</h3>
            </div>
        </div>
    </div>
    <div class="form-group" v-if="firstName && lastName && dateOfBirth">
        <h3>Adres en contactgegevens</h3>
        <p>
            <b
                >{{ FullName }}
                <span v-if="selectedGender">({{ selectedGender }})</span></b
            >
            <br />
            <span v-if="dateOfBirth">{{ FormattedDateOfBirth }}</span
            ><br />
            {{ citizenServiceNumber }}
        </p>
    </div>
    <div class="form-group">
        <h3>Over de overstap</h3>
        <ul>
            <li>
                {{ selectedReason }}
            </li>
            <li>
                Wij zeggen je huidige basisverzekering en aanvullende
                verzekering voor je op.
            </li>
        </ul>
    </div>
</template>

<script lang="ts">
import ZKDatepicker from '../atoms/ZKDatepicker/ZKDatepicker.vue';

interface OwnRiskOption {
    value: string;
    price: number;
    discount: boolean;
}

export default {
    name: 'ZKForm',
    components: { ZKDatepicker },
    props: {
        reasons: {
            type: Array,
            default: () => [
                {
                    value: 'Nieuwe werkgever met collectiviteit bij Zilveren Kruis'
                },
                { value: 'Overstappen per 1-1-2024 naar Zilveren Kruis' }
            ]
        },
        genders: {
            type: Array,
            default: () => [{ value: 'Man' }, { value: 'Vrouw' }]
        },
        insurances: {
            type: Array,
            default: () => [
                {
                    value: 'Basis Budget',
                    suggested: false,
                    price_per_year: 1393.26
                },
                {
                    value: 'Basis Zeker',
                    suggested: true,
                    price_per_year: 1483.5
                },
                {
                    value: 'Basis Exclusief (Restitutie)',
                    suggested: false,
                    price_per_year: 1624.6
                }
            ]
        },
        paymentTerms: {
            type: Array,
            default: () => [
                { value: 'per maand' },
                { value: 'per kwartaal' },
                { value: 'per jaar' }
            ]
        },
        OwnRiskOptions: {
            type: Array,
            default: () => [
                {
                    value: '€ 385 - verplicht eigen risico',
                    price: 385,
                    discount: null
                },
                {
                    value: '€ 885 - korting van € 22 per jaar',
                    price: 885,
                    discount: 22
                }
            ]
        },
        AdditionalInsurances: {
            type: Array,
            default: () => [
                { value: 'Geen aanvullende verzekering geselecteerd' },
                { value: 'Aanvullend 1 - € 21,38 per jaar', price: 21.38 },
                { value: 'Aanvullend 2 - € 85,06 per jaar', price: 85.06 },
                { value: 'Aanvullend 3 - € 198,63 per jaar', price: 198.63 },
                { value: 'Aanvullend 4 - € 359,73 per jaar', price: 359.73 }
            ]
        },
        DentalInsurances: {
            type: Array,
            default: () => [
                { value: 'Geen tandartsverzekering geselecteerd' },
                { value: 'Tand 1 - € 80,28 per jaar', price: 80.28 },
                { value: 'Tand 2 - € 221,65 per jaar', price: 221.65 },
                { value: 'Tand 3 - € 449,36 per jaar', price: 449.36 }
            ]
        }
    },
    computed: {
        IsInputInvalid() {
            if (this.isCitizenServiceNumberValid === false) return 'is-invalid';
            return null;
        },
        FullName(): string {
            return `${this.firstName} ${this.prefixes} ${this.lastName}`;
        },
        FormattedDateOfBirth(): string {
            if (this.dateOfBirth) {
                return this.dateOfBirth.toLocaleDateString('nl-NL', {
                    day: 'numeric',
                    month: 'long',
                    year: 'numeric'
                });
            }
            return '';
        },
        FinalBaseSum() {
            if (this.controle.selectedInsurance) {
                const pricePerYear =
                    this.controle.selectedInsurance.price_per_year;
                return this.calculatePricePerPaymentTerm(pricePerYear);
            }
            return null;
        },
        FinalSum() {
            const finalBaseSum = this.FinalBaseSum;
            const control = this.controle;
            const selectedAdditional = control.selectedAdditionalInsurance;
            const selectedDental = control.selectedDentalInsurance;

            const priceAdditional =
                this.AdditionalInsurances.filter(insurance => {
                    return insurance.value === selectedAdditional && insurance;
                })[0].price || 0;
            const priceDental =
                this.DentalInsurances.filter(insurance => {
                    return insurance.value === selectedDental && insurance;
                })[0].price || 0;

            return (
                finalBaseSum +
                this.calculatePricePerPaymentTerm(
                    priceAdditional + priceDental,
                    false
                )
            );
        },
        TotalDescription(): string {
            const selectedPaymentTerm = this.controle.selectedPaymentTerm;

            if (selectedPaymentTerm === this.paymentTerms[2].value) {
                return 'Totaal in 2023';
            }
            return `Totaal ${selectedPaymentTerm}, in 2023`;
        }
    },
    data() {
        return {
            selectedReason: this.reasons[1].value,
            firstName: '',
            prefixes: '',
            lastName: '',
            selectedGender: '',
            dateOfBirth: '',
            isCitizenServiceNumberValid: true,
            citizenServiceNumber: '',
            controle: {
                selectedInsurance: '',
                selectedPaymentTerm: this.paymentTerms[2].value,
                selectedOwnRisk: this.OwnRiskOptions[0].value,
                selectedAdditionalInsurance: this.AdditionalInsurances[0].value,
                selectedDentalInsurance: this.DentalInsurances[0].value
            }
        };
    },
    methods: {
        stringToLowerCase(str: string) {
            return str.replace(/\s+/g, '-').toLowerCase();
        },
        onInputBlur() {
            this.isCitizenServiceNumberValid =
                this.citizenServiceNumber &&
                this.citizenServiceNumber.length === 9
                    ? true
                    : false;

            return this.isCitizenServiceNumberValid;
        },
        numberToPrice(number: number) {
            const numberToFormat = new Intl.NumberFormat('nl-NL', {
                style: 'currency',
                currency: 'EUR'
            });

            return numberToFormat.format(number);
        },
        setInsuranceBase(insurance: { price_per_year: number }) {
            const selectedPaymentTerm = this.controle.selectedPaymentTerm;
            const insurancePrice = this.calculatePricePerPaymentTerm(
                insurance.price_per_year
            );

            return `${this.numberToPrice(
                insurancePrice as number
            )} ${selectedPaymentTerm}`;
        },
        calculatePricePerPaymentTerm(price: number, discount = true) {
            switch (this.controle.selectedPaymentTerm) {
                case this.paymentTerms[0].value:
                    return this.calculatePrice(price, 12, discount);
                case this.paymentTerms[1].value:
                    return this.calculatePrice(price, 4, discount);
                case this.paymentTerms[2].value:
                    return this.calculatePrice(price, 1, discount);
            }
        },
        calculatePrice(
            price: number,
            divide: number = 1,
            discount: boolean = true
        ) {
            const ownRisk = this.OwnRiskOptions[1] as OwnRiskOption;
            const selectedRisk = this.controle.selectedOwnRisk;
            let discountToGive: number | boolean;

            if (discount && selectedRisk === ownRisk.value) {
                discountToGive = divide
                    ? +ownRisk.discount / divide
                    : ownRisk.discount;
            } else {
                discountToGive = 0;
            }

            return divide
                ? price / divide - +discountToGive
                : price - +discountToGive;
        }
    }
};
</script>
