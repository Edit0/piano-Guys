<template>
  <div class="import">
         <div class="heard">
          <label @click="goback"><img src="/images/login/icon_arrow@2x.png"  style="width:60%"/></label>
            <label> {{title}}</label>
            <label></label>
          </div>
        <div class="lead-content" v-if="type==1">
           <van-tabs v-model="ETHactive">
            <van-tab title="keystore" disabled></van-tab>
            <van-tab :title="$t('message.Mnemonics')" disabled></van-tab>
            <van-tab :title="$t('message.PrivateKey')">
               
              <div class="eth-lead-group">
                <div class="noticetext">{{$t("message.sensitive")}}</div>
               <van-cell-group>
                 <van-field v-model="name"  :label="$t('message.WalletAlias')" :placeholder="$t('message.Pleaseenterthewalletalias')" />
                <van-field
                  v-model="Key"
                  :label="$t('message.Plaintext')"
                  type="textarea"
                  :placeholder="$t('message.Pleaseentertheplaintextprivatekey')"
                  rows="1"
                  size="large"
                  minHeight="100"
                 
                />

              </van-cell-group>
                <!-- <van-cell-group>
                 <van-field v-model="password" placeholder="请输入钱包密码" />
                 <van-field v-model="password1" placeholder="请重复输入密码" />
               </van-cell-group> -->
             
              </div>
            </van-tab>
            <van-tab :title="$t('message.Wallet')" disabled></van-tab>
          </van-tabs>
        </div>
        <div class="lead-content" v-if="type==2">
          <van-tabs v-model="BTCactive">
           
            <van-tab :title="$t('message.Mnemonics')" disabled></van-tab>
            <van-tab :title="$t('message.PrivateKey')">
               
              <div class="eth-lead-group">
                <div class="noticetext">{{$t("message.sensitive")}}</div>
               <van-cell-group>
                <van-field v-model="name"  :label="$t('message.WalletAlias')" :placeholder="$t('message.Pleaseenterthewalletalias')" />
                <van-field
                  v-model="Key"
                  :label="$t('message.Plaintext')"
                  type="textarea"
                   :placeholder="$t('message.Pleaseentertheplaintextprivatekey')"
                  rows="1"
                  size="large"
                  minHeight="100"
                 
                />
              </van-cell-group>
              
              <van-radio-group class="BTCradio" v-model="BTCradio" style="padding: 15px">
                <van-radio name="0" >{{$t("message.SegregatedWitness")}} </van-radio>
                <van-radio name="1">{{$t("message.General")}} </van-radio>
              </van-radio-group>
            
              </div>
            </van-tab>
          
          </van-tabs>
        </div>
        <div class="lead-content" v-if="type==3">
            <van-tabs v-model="Eosactive">
           
            
            <van-tab  :label="$t('message.PrivateKey')">
               
              <div class="eth-lead-group">
                <div class="noticetext">{{$t("message.sensitive")}}</div>
               <van-cell-group>
                <van-field v-model="name"  :label="$t('message.WalletAlias')" :placeholder="$t('message.Pleaseenterthewalletalias')"/>
                <van-field
                  v-model="Key"
                  :label="$t('message.Plaintext')"
                  type="textarea"
                 :placeholder="$t('message.Pleaseentertheplaintextprivatekey')"
                  rows="1"
                  size="large"
                  minHeight="100"    
                />
              </van-cell-group>
              </div>
            </van-tab>
             <van-tab :title="$t('message.PrivateKeyETH')" disabled></van-tab>
          </van-tabs>
             
          
        </div>
              <div class="lead-content" v-if="type==4">
            <van-tabs v-model="USTDactive">
           
            
            <van-tab title=" ERC20">
               
              <div class="eth-lead-group">
                <div class="noticetext">{{$t("message.sensitive")}}</div>
               <van-cell-group>
                <van-field  :label="$t('message.WalletAlias')"  v-model="name" :placeholder="$t('message.Pleaseenterthewalletalias')" />
                <van-field
                  v-model="Key"
                 :label="$t('message.Plaintext')"
                  type="textarea"
                  :placeholder="$t('message.Pleaseentertheplaintextprivatekey')"
                  rows="1"
                  size="large"
                  minHeight="100"
                  maxlength = "12"
                />
              </van-cell-group>
              
             
            
              </div>
            </van-tab>
             <van-tab title="OMNI" disabled></van-tab>
          </van-tabs>
             
          
        </div>
           <van-button type="large" @click="lead" class="eth-button" :disabled="!reMsg" :class="{'linear-button':reMsg}"   style="border:0;margin:30px auto 0;width:80%;display:block;border-radius:5px" >{{$t("message.Import")}}</van-button>
    </div>
</template>

<script>

import { importWallet  } from '@/api/wallet'
import { Toast } from 'vant';
import {ECPair,payments} from 'bitcoinjs-lib';
import {bip32}  from 'bip32'
const util = require('ethereumjs-util')
export default {
  data () {
    return {
      type:this.$route.query.id, //1.eth 2. etc 3. eos  4.ustd
      ETHactive:1,
      BTCactive:2,
      Eosactive:3,
      USTDactive:4,
      Key:'',
      password:'',
      BTCradio:'0',
      title:'',
      name:'',
      btcaddress:''
      
    }
  },
  created() {
   this.type= this.$route.query.id
   this.changeName()
 

// let addrr = util.privateToAddress('0x26bc034bb6af813ea646fbcde152d46925040fec0cc6ed95862d0dac92970de1')

    
    
  }, 

  methods: {
    goback() {
      this.$router.back()
    },
    lead(){
      console.log(this.$route.query.id)
       let _key = this.Key
       if (this.$route.query.id == '4' || this.$route.query.id == '1') {
         if(_key.slice(0,2).indexOf('0x') == -1){
           _key = '0x'+_key
         }
       }
       let obj ={
          type:this.type,
          pk:_key,
          uid:localStorage['_shop_userId'],
          name:this.name
        }
        if(!this.name){
          Toast.fail('钱包名称不能为空')
          return
        }

        if (this.type == 1) {
          try{
            let addrr = util.publicToAddress(util.privateToPublic(this.Key)).toString('hex')
            obj.address = addrr 
          }catch(e){
            Toast.fail('导入失败，请检查私钥') 
            return
          }

        }



// //  // 普通1
// let keyPair = ECPair.fromWIF('Kxr9tQED9H44gCmp6HAdmemAzU3n84H3dGkuWTKvE23JgHMW8gct')
// const p2pkh = payments.p2pkh({ pubkey: keyPair.publicKey });
// let addr = p2pkh.address;
// console.log(addr);

// // SW 0
//   keyPair = ECPair.fromWIF('KwvFAjTd8Nf8DeRAMiB5gaS9SnKP8rTJ31CN5DV5DXLKBiy55WfS');
//   let p2pkh1 = payments.p2sh({
//     redeem: payments.p2wpkh({ pubkey: keyPair.publicKey  })
//   });
// console.log(p2pkh1.address);

        if(this.type == 2) {
          obj.btctype= this.BTCradio
          if (this.BTCradio == 0) {
            // sw
            try{
               let  keyPair = ECPair.fromWIF(this.Key);
              let p2pkh1 = payments.p2sh({
                redeem: payments.p2wpkh({ pubkey: keyPair.publicKey  })
              });
              console.log(p2pkh1.address);
              obj.btcaddress = p2pkh1.address
            }catch (e) {
              Toast.fail('错误的私钥')
              return
            }

          }
          if (this.BTCradio == 1 ) {
            // putong
            try{
             let keyPair = ECPair.fromWIF(this.Key)
            const p2pkh = payments.p2pkh({ pubkey: keyPair.publicKey });
            let addr = p2pkh.address;
            obj.btcaddress = addr
            console.log(addr);   
            } catch (e) {
               Toast.fail('错误的私钥')
              return 
            }
  
          }
        }
   
        importWallet(obj).then(res=>{
           if(res.status== 200) {
             Toast.success('导入成功')
              
             setTimeout(() => {
                  this.$router.push({
                    path:'/wallet'
                  })
              }, 500);
            
           }else {
              Toast.fail(res.msg)
           }

        })
    },
     changeName(){
       switch(this.type) {
          case 1:
              this.title = 'ETH'
              break;
          case 2:
             this.title = 'BTC'
              break;
          case 3:
              this.title = 'EOS'
            break;
          case 4:
              this.title = 'USTD'
          break;
    } 
      },
    },
     computed:{
      reMsg () {
        if( this.Key) {
            return true
          }else {
            return false
          }
      }
  },
    onlead(){
     this.show = false
     this.leadVisabled = true
    },
   
};
</script>
<style scoped lang="stylus" >

  .topbg
    background:#fff
  .heard
    font-size:17px
    color:rgb(51,51,51)
    display:flex
    align-items: center
    justify-content: space-between
    height:44px
    label:first-child,label:last-child
      width:10%
      height: 100%
      padding: 0 3%
      display: flex
      align-items: center
      justify-content: center
    .select
      display:flex
      align-items:center
      justify-content: center
      border:1px solid rgb(229,229,229)
      border-radius:15px
      padding:2px 0
      span
        margin-right:10px
      img
        width:20%
  
</style>
<style scope>
  .noticetext {
      background: #f9f9f9;
      font-size: 12px;
      padding: 15px 15px;
      color: #999;
      text-align: center;
  }
  .eth-btn {
    background: #f1f1f1;

  }
</style>
<style>
 .lead-content .van-field--min-height .van-field__control {
   border: 1px solid #eee;
   border-radius: 3px;
   padding: 5px;
   min-height: 80px
 }
 .lead-content .van-radio {
   display: block;
  width: 50%;
  float: left
 }
 .BTCradio .van-radio__icon {
   float:left
 }
 .eth-lead-group .van-cell--large .van-cell__title{
   font-size:14px
 }
</style>

