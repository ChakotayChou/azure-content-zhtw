- **原則式 VPN 類型︰**原則式 VPN 先前在傳統部署模型內稱為靜態路由閘道。原則式 VPN 會根據使用內部部署網路與 Azure VNet 之間的位址首碼組合所設定的 IPsec 原則，透過 IPsec 通道加密和導向封包。原則 (或流量選取器) 通常定義為 VPN 裝置組態中的存取清單。原則式 VPN 類型的值是 PolicyBased。使用高效能 SKU 時，不支援以原則為基礎的 VPN 類型。

- **路由式 VPN 類型︰**路由式 VPN 先前在傳統部署模型內稱為動態路由閘道。路由式 Vpn 會使用 IP 轉送或路由表中的「路由」，直接封包至其對應的通道介面。然後，通道介面會加密或解密輸入和輸出通道的封包。路由式 VPN 的原則 (或流量選取器) 會設定為任何對任何 (或萬用字元)。路由式 VPN 類型的值是 RouteBased。

<!---HONumber=AcomDC_0727_2016-->