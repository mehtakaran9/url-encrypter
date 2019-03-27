<template>
    <div class="container">
        <h1>URL ENCRYPTER</h1>
        <b-form>
            <div class="row">
                <div class="col-10">
                    <b-form-group>
                        <b-form-input type="text" id="url_input" v-model="url" placeholder="Insert Url Here.."/>
                    </b-form-group>
                </div>
                <div class="col-2">
                    <b-button @click="clicked" variant="success">Encrypt Url</b-button>
                </div>
            </div>
            <div class="row">
                <div class="col-10">
                    <b-form-group>
                        <b-form-textarea type="text" id="url_output" v-model="encryptedUrl" disabled></b-form-textarea>
                    </b-form-group>
                </div>
                <div class="col-2">
                    <button type="button" id="copy-clip-board"
                        v-clipboard:copy="encryptedUrl"
                        v-clipboard:success="onCopy"
                        v-clipboard:error="onError"><i class="fa fa-copy" style="font-size:24px;margin:8px;"></i></button>
                </div>
            </div>
        </b-form>
    </div>
</template>
<script>
import Vue from 'vue'
var CryptoJS = require("crypto-js");
var aesjs = require('aes-js');
var CTR = require("crypto-js/mode-ctr");
var AES = require("crypto-js/aes");
var SHA256 = require("crypto-js/sha256");
var NoPadding = require("crypto-js/pad-nopadding");

export default {
    name : "url-encrypter",
    data() {
        return {
            url : null,
            encryptedUrl : null,
            key : "u/Zu9rtcqbDsXUnV5Zaq4g==",
            initVector : "2D3r9ZVzEYYfbb93/aXK2w=="
        }
    },
    methods : {
        clicked () {
            var rkEncryptionKey = CryptoJS.enc.Base64.parse(this.key);
            var rkEncryptionIv = CryptoJS.enc.Base64.parse(this.initVector);
            var utf8Stringified = CryptoJS.enc.Utf8.parse(this.url);
            var text = CryptoJS.AES.encrypt(utf8Stringified, rkEncryptionKey, {
                mode: CryptoJS.mode.CBC,
                iv: rkEncryptionIv,
                padding: CryptoJS.pad.Pkcs7
            });
            this.encryptedUrl = text.ciphertext.toString(CryptoJS.enc.Base64)
        },
        onCopy (evt) {
            Vue.notify({
                group: 'copy',
                type: 'success',
                title: 'Encrypted Url Copied'
            })
        },
        onError () {
            Vue.notify({
                group: 'copy',
                type: 'error',
                title: 'Encrypted Url is not copied'
            })
        },stringToBytes(str) {
            var ch, st, re = [];
            for (var i = 0; i < str.length; i++ ) {
                ch = str.charCodeAt(i);  // get char 
                st = [];                 // set up "stack"
                do {
                st.push( ch & 0xFF );  // push byte to stack
                ch = ch >> 8;          // shift value down by 1 byte
                }  
                while ( ch );
                // add stack contents to result
                // done because chars have "wrong" endianness
                re = re.concat( st.reverse() );
            }
            // return an array of bytes
            return re;
        }    
     }
}
</script>
<style>
    #copy-clip-board{
        border-radius: 10%;
        width: auto;
        height: auto;
        background-color: #d1d1d1;
        left:0;
    }
    #copy-clip-board :hover {
        border-color: #000000;
    }
</style>

