<script setup lang="ts">
import { BarcodeScanner } from '@capacitor-community/barcode-scanner'
import { Dialog } from '@capacitor/dialog'

let qrContent: any;

const startScan = () => {
    BarcodeScanner.hideBackground()
    BarcodeScanner.startScan()
        .then(async result => {
            if (!result.content) throw 'El código escaneado no tenía contenido'
            qrContent = JSON.parse(result.content)
            await Dialog.alert({
                title: 'QR perteneciente a:',
                message: qrContent.nombre
            })
            stopScan()
            startScan()
        })
        .catch(async error => {
            await Dialog.alert({
                title: 'Error',
                message: error
            })
        })
}

const stopScan = () => {
    BarcodeScanner.showBackground()
    BarcodeScanner.stopScan()
}

const checkPermission = () => {
    return new Promise(async (resolve, reject) => {
        const status = await BarcodeScanner.checkPermission({ force: true })
        if (status.granted) resolve(status)
        reject(status)
    })
}
checkPermission()
    .then(status => {
        startScan()
    })
    .catch(status => {
        stopScan()
    })
</script>

<template>
    <div>
        <!-- <h1>QR scanner</h1>
        <button class="btn btn-primary" @click="startScan">Scan QR code</button>
        <p v-if="qrContent">{{ qrContent }}</p> -->
    </div>
</template>
