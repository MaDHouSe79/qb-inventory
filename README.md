<p align="center">
    <img width="140" src="https://icons.iconarchive.com/icons/iconarchive/red-orb-alphabet/128/Letter-M-icon.png" />  
    <h1 align="center">Hi 👋, I'm MaDHouSe</h1>
    <h3 align="center">A passionate allround developer </h3>    
</p>

<p align="center">
    <a href="https://github.com/MaDHouSe79/qb-inventory/issues">
        <img src="https://img.shields.io/github/issues/MaDHouSe79/qb-inventory"/> 
    </a>
    <a href="https://github.com/MaDHouSe79/qb-inventory/watchers">
        <img src="https://img.shields.io/github/watchers/MaDHouSe79/qb-inventory"/> 
    </a> 
    <a href="https://github.com/MaDHouSe79/qb-inventory/network/members">
        <img src="https://img.shields.io/github/forks/MaDHouSe79/qb-inventory"/> 
    </a>  
    <a href="https://github.com/MaDHouSe79/qb-inventory/stargazers">
        <img src="https://img.shields.io/github/stars/MaDHouSe79/qb-inventory?color=white"/> 
    </a>
    <a href="https://github.com/MaDHouSe79/qb-inventory/blob/main/LICENSE">
        <img src="https://img.shields.io/github/license/MaDHouSe79/qb-inventory?color=black"/> 
    </a>      
</p>

# My Youtube Channel
- [Subscribe](https://www.youtube.com/c/@MaDHouSe79) 

# qb-inventory 2.0.0
- Edit by MaDHouSe79 so you can use cash as item.
- you don't need `mh-cashasitem` cause this `qb-inventory` handles the items and money.
- no extra scripts needed this `qb-inventory` `2.0.0` is cash item ready.

# Install
- Backup your own `qb-inventory` 2.0.0 and replace it with this `qb-inventory` 2.0.0
- Make sure you add all your invnetory images in this inventory images folder, don't delete the cash item images.

# Use the giveitem command
- /giveitem [id] cash [amount]
- /giveitem [id] black_money [amount]
- /giveitem [id] crypto [amount]
 
# Images are already included
![alttext](https://github.com/MaDHouSe79/mh-cashasitem/blob/main/image/cash.png?raw=true)
![alttext](https://github.com/MaDHouSe79/mh-cashasitem/blob/main/image/black_money.png?raw=true)
![alttext](https://github.com/MaDHouSe79/mh-cashasitem/blob/main/image/crypto.png?raw=true)

# Video
- [Video](https://www.youtube.com/watch?v=-GYOfPkXM0A)

# Note for using blackmoney
- first you need to edit the qb-core/config.lua to this below add black_money to this tables
```lua
QBConfig.Money.MoneyTypes = { cash = 500, bank = 5000, crypto = 0, black_money = 0 } -- type = startamount - Add or remove money types for your server (for ex. blackmoney = 0), remember once added it will not be removed from the database!
QBConfig.Money.DontAllowMinus = { 'cash', 'crypto', 'black_money' } -- Money that is not allowed going in minus
```

# QB Shared Items
```lua
cash = { name = 'cash', label = 'Cash', weight = 0, type = 'item', image = 'cash.png', unique = false, useable = false, shouldClose = true, combinable = nil, description = 'Cash'  },
black_money = { name = 'black_money', label = 'Black Money', weight = 0, type = 'item', image = 'black_money.png', unique = false, useable = false, shouldClose = true, combinable = nil, description = 'Black Money?' },
crypto = { name = 'crypto', label = 'Crypto', weight = 0, type = 'item', image = 'crypto.png', unique = false, useable = false, shouldClose = true, combinable = nil, description = 'Crypto' },
```

# To disable the hub money change
- Replace this code in `qb-core/server/player.lua` around line 312
```lua
function self.Functions.AddMoney(moneytype, amount, reason)
    reason = reason or 'unknown'
    moneytype = moneytype:lower()
    amount = tonumber(amount)
    if amount < 0 then return end
    if not self.PlayerData.money[moneytype] then return false end
    self.PlayerData.money[moneytype] = self.PlayerData.money[moneytype] + amount

    if not self.Offline then
        self.Functions.UpdatePlayerData()
        if amount > 100000 then
            TriggerEvent('qb-log:server:CreateLog', 'playermoney', 'AddMoney', 'lightgreen', '**' .. GetPlayerName(self.PlayerData.source) .. ' (citizenid: ' .. self.PlayerData.citizenid .. ' | id: ' .. self.PlayerData.source .. ')** $' .. amount .. ' (' .. moneytype .. ') added, new ' .. moneytype .. ' balance: ' .. self.PlayerData.money[moneytype] .. ' reason: ' .. reason, true)
        else
            TriggerEvent('qb-log:server:CreateLog', 'playermoney', 'AddMoney', 'lightgreen', '**' .. GetPlayerName(self.PlayerData.source) .. ' (citizenid: ' .. self.PlayerData.citizenid .. ' | id: ' .. self.PlayerData.source .. ')** $' .. amount .. ' (' .. moneytype .. ') added, new ' .. moneytype .. ' balance: ' .. self.PlayerData.money[moneytype] .. ' reason: ' .. reason)
        end
        --TriggerClientEvent('hud:client:OnMoneyChange', self.PlayerData.source, moneytype, amount, false)
        TriggerClientEvent('QBCore:Client:OnMoneyChange', self.PlayerData.source, moneytype, amount, 'add', reason)
        TriggerEvent('QBCore:Server:OnMoneyChange', self.PlayerData.source, moneytype, amount, 'add', reason)
    end

    return true
end

function self.Functions.RemoveMoney(moneytype, amount, reason)
    reason = reason or 'unknown'
    moneytype = moneytype:lower()
    amount = tonumber(amount)
    if amount < 0 then return end
    if not self.PlayerData.money[moneytype] then return false end
    for _, mtype in pairs(QBCore.Config.Money.DontAllowMinus) do
        if mtype == moneytype then
            if (self.PlayerData.money[moneytype] - amount) < 0 then
                return false
            end
        end
    end
    if self.PlayerData.money[moneytype] - amount < QBCore.Config.Money.MinusLimit then return false end
    self.PlayerData.money[moneytype] = self.PlayerData.money[moneytype] - amount

    if not self.Offline then
        self.Functions.UpdatePlayerData()
        if amount > 100000 then
            TriggerEvent('qb-log:server:CreateLog', 'playermoney', 'RemoveMoney', 'red', '**' .. GetPlayerName(self.PlayerData.source) .. ' (citizenid: ' .. self.PlayerData.citizenid .. ' | id: ' .. self.PlayerData.source .. ')** $' .. amount .. ' (' .. moneytype .. ') removed, new ' .. moneytype .. ' balance: ' .. self.PlayerData.money[moneytype] .. ' reason: ' .. reason, true)
        else
            TriggerEvent('qb-log:server:CreateLog', 'playermoney', 'RemoveMoney', 'red', '**' .. GetPlayerName(self.PlayerData.source) .. ' (citizenid: ' .. self.PlayerData.citizenid .. ' | id: ' .. self.PlayerData.source .. ')** $' .. amount .. ' (' .. moneytype .. ') removed, new ' .. moneytype .. ' balance: ' .. self.PlayerData.money[moneytype] .. ' reason: ' .. reason)
        end
        --TriggerClientEvent('hud:client:OnMoneyChange', self.PlayerData.source, moneytype, amount, true)
        if moneytype == 'bank' then
            TriggerClientEvent('qb-phone:client:RemoveBankMoney', self.PlayerData.source, amount)
        end
        TriggerClientEvent('QBCore:Client:OnMoneyChange', self.PlayerData.source, moneytype, amount, 'remove', reason)
        TriggerEvent('QBCore:Server:OnMoneyChange', self.PlayerData.source, moneytype, amount, 'remove', reason)
    end

    return true
end

function self.Functions.SetMoney(moneytype, amount, reason)
    reason = reason or 'unknown'
    moneytype = moneytype:lower()
    amount = tonumber(amount)
    if amount < 0 then return false end
    if not self.PlayerData.money[moneytype] then return false end
    local difference = amount - self.PlayerData.money[moneytype]
    self.PlayerData.money[moneytype] = amount

    if not self.Offline then
        self.Functions.UpdatePlayerData()
        TriggerEvent('qb-log:server:CreateLog', 'playermoney', 'SetMoney', 'green', '**' .. GetPlayerName(self.PlayerData.source) .. ' (citizenid: ' .. self.PlayerData.citizenid .. ' | id: ' .. self.PlayerData.source .. ')** $' .. amount .. ' (' .. moneytype .. ') set, new ' .. moneytype .. ' balance: ' .. self.PlayerData.money[moneytype] .. ' reason: ' .. reason)
        --TriggerClientEvent('hud:client:OnMoneyChange', self.PlayerData.source, moneytype, math.abs(difference), difference < 0)
        TriggerClientEvent('QBCore:Client:OnMoneyChange', self.PlayerData.source, moneytype, amount, 'set', reason)
        TriggerEvent('QBCore:Server:OnMoneyChange', self.PlayerData.source, moneytype, amount, 'set', reason)
    end

    return true
end
```

## **INSTALL FOR QB HUD**
# Add in top of qb-hud/client.lua
- around line 15
```lua
local blackAmount = 0
local cryptoAmount = 0
```

# Replace this code below in qb-hud/client.lua
- around line 863
```lua
RegisterNetEvent('hud:client:ShowAccounts', function(type, amount)
    if amount == nil then amount = 0 end
    if type == 'cash' then
        SendNUIMessage({
            action = 'show',
            type = 'cash',
            cash = Round(amount)
        })
    elseif type == 'black_money' then
        SendNUIMessage({
            action = 'show',
            type = 'blackmoney',
            blackmoney = Round(amount)
        })
    elseif type == 'crypto' then
        SendNUIMessage({
            action = 'show',
            type = 'crypto',
            crypto = Round(amount)
        })
    else
        SendNUIMessage({
            action = 'show',
            type = 'bank',
            bank = Round(amount)
        })
    end
end)
```

# Replace this code below in qb-hud/client.lua
- around line 886
```lua
RegisterNetEvent('hud:client:OnMoneyChange', function(type, amount, isMinus)
    cashAmount = PlayerData.money['cash']
    bankAmount = PlayerData.money['bank']
    blackAmount = PlayerData.money['black_money']
    cryptoAmount = PlayerData.money['crypto']
    SendNUIMessage({
        action = 'updatemoney',
        cash = Round(cashAmount),
        bank = Round(bankAmount),
        blackmoney = Round(blackAmount),
        crypto = Round(cryptoAmount),
        amount = Round(amount),
        minus = isMinus,
        type = type
    })
end)
```

# Replace this code below in qb-hud/html/app.js
- around line 622
```js
// MONEY HUD
const moneyHud = Vue.createApp({
    data() {
        return {
            cash: 0,
            bank: 0,
            blackmoney: 0,
            crypto: 0,
            amount: 0,
            plus: false,
            minus: false,
            showCash: false,
            showBank: false,
            showBlack: false,
            showCrypto: false,
            showUpdate: false,
        };
    },
    destroyed() {
        window.removeEventListener("message", this.listener);
    },
    mounted() {
        this.listener = window.addEventListener("message", (event) => {
            switch (event.data.action) {
                case "showconstant":
                    this.showConstant(event.data);
                    break;
                case "updatemoney":
                    this.update(event.data);
                    break;
                case "show":
                    this.showAccounts(event.data);
                    break;
            }
        });
    },
    methods: {
        // CONFIGURE YOUR CURRENCY HERE
        // https://www.w3schools.com/tags/ref_language_codes.asp LANGUAGE CODES
        // https://www.w3schools.com/tags/ref_country_codes.asp COUNTRY CODES
        formatMoney(value) {
            const formatter = new Intl.NumberFormat("en-US", {
                style: "currency",
                currency: "USD",
                minimumFractionDigits: 0,
            });
            return formatter.format(value);
        },
        showConstant(data) {
            this.showCash = true;
            this.showBank = true;
            this.showBlack = true;
            this.showCrypto = true;
            this.cash = data.cash;
            this.bank = data.bank;
            this.blackmoney = data.blackmoney;
            this.crypto = data.crypto;
        },
        update(data) {
            this.showUpdate = true;
            this.amount = data.amount;
            this.bank = data.bank;
            this.cash = data.cash;
            this.blackmoney = data.blackmoney;
            this.crypto = data.crypto;
            this.minus = data.minus;
            this.plus = data.plus;
            if (data.type === "cash") {
                if (data.minus) {
                    this.showCash = true;
                    this.minus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showCash = false), 2000);
                } else {
                    this.showCash = true;
                    this.plus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showCash = false), 2000);
                }
            }
            if (data.type === "bank") {
                if (data.minus) {
                    this.showBank = true;
                    this.minus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showBank = false), 2000);
                } else {
                    this.showBank = true;
                    this.plus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showBank = false), 2000);
                }
            }
            if (data.type === "black_money") {
                if (data.minus) {
                    this.showBlack = true;
                    this.minus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showBlack = false), 2000);
                } else {
                    this.showBlack = true;
                    this.plus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showBlack = false), 2000);
                }
            }
            if (data.type === "crypto") {
                if (data.minus) {
                    this.showCrypto = true;
                    this.minus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showCrypto = false), 2000);
                } else {
                    this.showCrypto = true;
                    this.plus = true;
                    setTimeout(() => (this.showUpdate = false), 1000);
                    setTimeout(() => (this.showCrypto = false), 2000);
                }
            }
        },
        showAccounts(data) {
            if (data.type === "cash" && !this.showCash) {
                this.showCash = true;
                this.cash = data.cash;
                setTimeout(() => (this.showCash = false), 3500);
            } else if (data.type === "bank" && !this.showBank) {
                this.showBank = true;
                this.bank = data.bank;
                setTimeout(() => (this.showBank = false), 3500);
            }else if (data.type === "blackmoney" && !this.showBlack) {
                this.showBlack = true;
                this.blackmoney = data.blackmoney;
                setTimeout(() => (this.showBlack = false), 3500);
            }else if (data.type === "crypto" && !this.showCrypto) {
                this.showCrypto = true;
                this.crypto = data.crypto;
                setTimeout(() => (this.showCrypto = false), 3500);
            }
        },
    },
}).mount("#money-container");
```

# Add this code below in qb-hud/html/index.html
- around line 205
```html
<div id="money-cash">
    <transition name="slide-fade">
        <p v-if="showBlack"><span id="sign">$&nbsp;</span><span id="money">{{(blackmoney)}}</span></p>
    </transition>
</div>

<div id="money-cash">
    <transition name="slide-fade">
        <p v-if="showCrypto"><span id="sign">₿&nbsp;</span><span id="money">{{(crypto)}}</span></p>
    </transition>
</div>
```
