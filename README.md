This is a new [**React Native**](https://reactnative.dev) project, bootstrapped using [`@react-native-community/cli`](https://github.com/react-native-community/cli).

# Getting Started

>**Note**: Make sure you have completed the [React Native - Environment Setup](https://reactnative.dev/docs/environment-setup) instructions till "Creating a new application" step, before proceeding.

## Step 1: Start the Metro Server

First, you will need to start **Metro**, the JavaScript _bundler_ that ships _with_ React Native.

To start Metro, run the following command from the _root_ of your React Native project:

```bash
# using npm
npm start

# OR using Yarn
yarn start
```

## Step 2: Start your Application

Let Metro Bundler run in its _own_ terminal. Open a _new_ terminal from the _root_ of your React Native project. Run the following command to start your _Android_ or _iOS_ app:

### For Android

```bash
# using npm
npm run android

# OR using Yarn
yarn android
```

### For iOS

```bash
# using npm
npm run ios
cd ios && pod install

# OR using Yarn
yarn ios
cd ios && pod install
```

If everything is set up _correctly_, you should see your new app running in your _Android Emulator_ or _iOS Simulator_ shortly provided you have set up your emulator/simulator correctly.

This is one way to run your app — you can also run it directly from within Android Studio and Xcode respectively.

# Hướng Dẫn Tương Tác Web3, DApp và Blockchain - Truy Xuất nguồn gốc nông sản

> **LƯU Ý QUAN TRỌNG**: Đây chỉ là tài liệu hướng dẫn mẫu, cung cấp các ví dụ code và cách thực hiện. Tài liệu này không phải là code thực tế đang chạy trong ứng dụng, mà là hướng dẫn cách triển khai các tính năng Web3 và blockchain trong dự án . Các đoạn code được trình bày nhằm mục đích minh họa và có thể cần điều chỉnh để phù hợp với yêu cầu cụ thể của dự án.

## Mục Lục

1. [Tổng Quan](#tổng-quan)
2. [Yêu Cầu Hệ Thống và Cài Đặt](#yêu-cầu-hệ-thống-và-cài-đặt)
   - [Yêu Cầu Hệ Thống](#yêu-cầu-hệ-thống)
   - [Cài Đặt Dependencies](#cài-đặt-dependencies)
   - [Bước Đầu Tiên](#bước-đầu-tiên)
3. [Các Thư Viện và Dependencies](#các-thư-viện-và-dependencies)
   - [Ethers.js](#1-ethersjs)
   - [Reown AppKit](#2-reownappkit-ethers-react-native)
   - [WalletConnect Compat](#3-walletconnectreact-native-compat)
   - [React Query](#4-tanstackreact-query)
   - [Axios](#5-axios)
4. [Cấu Hình WalletConnect và Blockchain](#cấu-hình-walletconnect-và-blockchain)
   - [Cấu Hình Mạng Blockchain](#cấu-hình-mạng-appjs)
   - [Code Mẫu Hoàn Chỉnh](#code-mẫu-hoàn-chỉnh-từ-reown-repository)
   - [Cách Lấy Project ID](#cách-lấy-walletconnect-project-id)
5. [Kiến Trúc Hệ Thống](#kiến-trúc-hệ-thống)
   - [Vấn Đề và Giải Pháp Blockchain](#vấn-đề-cần-giải-quyết-và-giải-pháp-blockchain)
   - [Cấu Trúc File](#cấu-trúc-file)
   - [Mapping Vấn Đề → Smart Contract](#kiến-trúc-giải-quyết-vấn-đề-nông-nghiệp)
   - [Luồng Hoạt Động Tổng Quan](#luồng-hoạt-động-frontend--pionewallet--blockchain--backend)
   - [Chi Tiết Kiến Trúc](#chi-tiết-từng-bước-kiến-trúc-thực-tế)
6. [ABI và Smart Contract Basics](#abi-application-binary-interface)
   - [Khái Niệm ABI](#khái-niệm-abi)
   - [Vai Trò của ABI](#vai-trò-của-abi)
   - [Cách Lấy ABI](#cách-lấy-abi)
   - [Quản Lý Địa Chỉ Contract](#quản-lý-địa-chỉ-contract-động)
7. [Tương Tác Với Smart Contract](#tương-tác-với-smart-contract)
   - [Cấu Hình Contract](#1-cấu-hình-contract-srcservicesblockchainjs)
   - [Kết Nối Ví và Provider](#2-kết-nối-ví-và-lấy-provider)
   - [Đọc Dữ Liệu](#3-đọc-dữ-liệu-từ-contract-read-operations)
   - [Ghi Dữ Liệu](#4-ghi-dữ-liệu-lên-contract-write-operations)
8. [Quản Lý Ví](#quản-lý-ví-wallet-management)
   - [Kết Nối Ví với Reown](#1-kết-nối-ví-với-reown)
   - [Sử Dụng Components](#2-sử-dụng-component-của-reown-để-kết-nối-ví)
   - [Components Nâng Cao](#3-các-component-khác-của-reown)
9. [Quản Lý State và Giao Dịch](#quản-lý-state-và-giao-dịch)
   - [Theo Dõi Lịch Sử Giao Dịch](#1-theo-dõi-lịch-sử-giao-dịch-trên-pione-zero-chain)
   - [Custom Hooks](#2-custom-hook-cho-blockchain-operations)
   - [React Query Integration](#3-quản-lý-state-với-react-query)
   - [Ví Dụ Thực Tế](#sử-dụng-trong-component)
10. [Tài Liệu Tham Khảo](#tài-liệu-tham-khảo)

## Tổng Quan

Tài liệu này hướng dẫn cách tích hợp Web3, DApp và blockchain vào ứng dụng React Native "Truy Xuất nguồn gốc nông sản". Tài liệu tập trung vào việc sử dụng các thư viện như Ethers.js, Reown AppKit và WalletConnect để tương tác với blockchain Pione Zero Chain.

## Yêu Cầu Hệ Thống và Cài Đặt

### Yêu Cầu Hệ Thống

- **Node.js**: >= 20.x.x
- **React Native CLI & Expo**
- **Android Studio** (cho Android) hoặc **Xcode** (cho iOS)
- **Ví crypto**: PioneWallet, MetaMask hoặc ví tương thích WalletConnect

### Cài Đặt Dependencies

- **Cài đặt Reown AppKit và các package cần thiết**: https://docs.reown.com/appkit/react-native/core/installation

```bash
# Cài đặt các package cần thiết
npm install ethers@6.13.5
npm install @reown/appkit-ethers-react-native
npm install @walletconnect/react-native-compat
npm install @tanstack/react-query
npm install axios

# Cho iOS (nếu cần)
cd ios && pod install
```

### Bước Đầu Tiên

1. **Tạo ví crypto**: Cài đặt PioneWallet hoặc MetaMask
2. **Lấy testnet tokens**: Truy cập faucet để lấy PZO tokens miễn phí : https://dex.pionechain.com/testnet/faucet
3. **Kết nối với Pione Zero Chain**: Thêm network vào ví (nếu dùng MetaMask)
4. **Tạo project**: Tạo project làm react native cli hay expo
5. **Chạy ứng dụng**

## Các Thư Viện và Dependencies

#### 1. **Ethers.js**

- **Mục đích**: Thư viện chính để tương tác với Ethereum và các blockchain tương thích
- **Chức năng**:
  - Kết nối với RPC endpoints
  - Tạo và quản lý contract instances
  - Thực hiện giao dịch blockchain
  - Đọc dữ liệu từ smart contracts

#### 2. **@reown/appkit-ethers-react-native**

- **Mục đích**: SDK để tích hợp WalletConnect và quản lý ví trong React Native
- **Chức năng**:
  - Kết nối với các ví crypto (MetaMask, Trust Wallet, etc.)
  - Quản lý phiên kết nối ví
  - Cung cấp UI components cho wallet connection

#### 3. **@walletconnect/react-native-compat**

- **Mục đích**: Đảm bảo tương thích WalletConnect với React Native
- **Chức năng**: Polyfill các API cần thiết cho WalletConnect

#### 4. **@tanstack/react-query**

- **Mục đích**: Quản lý state và cache dữ liệu blockchain
- **Chức năng**:
  - Caching dữ liệu blockchain
  - Auto-refetching dữ liệu khi cần
  - Invalidating cache sau khi thực hiện giao dịch

#### 5. **axios**

- **Mục đích**: Thư viện để thực hiện HTTP requests
- **Chức năng**:
  - Thực hiện HTTP requests
  - Xử lý response từ server
  - Xử lý error

## Cấu Hình WalletConnect và Blockchain

### Cấu Hình Mạng (App.js)

- **Cấu hình WalletConnect chi tiết**: https://docs.reown.com/appkit/react-native/core/installation#ethers-3

```javascript
// Testnet Configuration
export const testnet = {
  chainId: 5080,
  name: 'Pione Zero',
  currency: 'PZO',
  explorerUrl: 'https://zeroscan.org',
  rpcUrl: 'https://rpc.zeroscan.org',
};
```

### Code Mẫu Hoàn Chỉnh từ Reown Repository

**Nguồn tham khảo**: [W3MEthers Example - Reown React Native Examples](https://github.com/reown-com/react-native-examples/tree/main/dapps/W3MEthers)

##### 1. Package.json Dependencies

```json
{
  "dependencies": {
    "@react-native-async-storage/async-storage": "1.21.0",
    "@react-native-clipboard/clipboard": "1.13.2",
    "@react-native-community/netinfo": "11.2.1",
    "@reown/appkit-ethers-react-native": "1.3.0",
    "@walletconnect/react-native-compat": "2.21.5",
    "ethers": "6.15.0",
    "react": "18.2.0",
    "react-native": "0.73.4",
    "react-native-get-random-values": "1.10.0",
    "react-native-mmkv": "2.11.0",
    "react-native-modal": "14.0.0-rc.1",
    "react-native-svg": "14.1.0",
    "react-native-webview": "13.8.2"
  }
}
```

##### 2. Cấu Hình App.tsx Hoàn Chỉnh

```javascript
import '@walletconnect/react-native-compat';

import React, {useEffect} from 'react';
import {Linking, SafeAreaView, StyleSheet} from 'react-native';
import Clipboard from '@react-native-clipboard/clipboard';

import {
  createAppKit,
  defaultConfig,
  AppKitButton,
  AppKit,
} from '@reown/appkit-ethers-react-native';
import {FlexView, Text} from '@reown/appkit-ui-react-native';
import {MMKV} from 'react-native-mmkv';

// Import các component tương tác blockchain
import {SignMessage} from './views/SignMessage';
import {SendTransaction} from './views/SendTransaction';
import {ReadContract} from './views/ReadContract';
import {WriteContract} from './views/WriteContract';
import {SignTypedDataV4} from './views/SignTypedDataV4';

// Cấu hình chains (thay thế bằng Pione Chain)
const pioneMainnet = {
  chainId: 5090,
  name: 'Pione Chain',
  currency: 'PIO',
  explorerUrl: 'https://scan.pionechain.com',
  rpcUrl: 'https://rpc.pionechain.com',
};

const pioneTestnet = {
  chainId: 5080,
  name: 'Pione Zero',
  currency: 'PZO',
  explorerUrl: 'https://zeroscan.org',
  rpcUrl: 'https://rpc.zeroscan.org',
};

// 1. Lấy projectId từ https://cloud.reown.com
const projectId = 'YOUR_PROJECT_ID';

// 2. Định nghĩa chains
const chains = [pioneMainnet, pioneTestnet];

// 3. Tạo config
const metadata = {
  name: 'PioneFarm DApp',
  description: 'Ứng dụng truy xuất nguồn gốc nông sản',
  url: 'https://pionefarm.com',
  icons: ['https://pionefarm.com/logo.png'],
  redirect: {
    native: 'pionefarm://',
  },
};

const config = defaultConfig({
  metadata,
});

// Clipboard client cho copy/paste
const clipboardClient = {
  setString: async (value: string) => {
    Clipboard.setString(value);
  },
};

// Custom wallets (tùy chọn)
const customWallets = [
  {
    id: 'pione-wallet',
    name: 'PioneWallet',
    image_url: 'https://pionewallet.com/logo.png',
    mobile_link: 'tobewallet://',
  },
];

// 4. Tạo AppKit
createAppKit({
  projectId,
  metadata,
  chains,
  config,
  customWallets,
  clipboardClient,
  features: {
    swaps: false, // Tắt swap nếu không cần
    onramp: false, // Tắt onramp nếu không cần
  },
});

function App(): React.JSX.Element {
  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.title} variant="large-600">
        PioneFarm DApp
      </Text>
      <FlexView style={styles.buttonContainer}>
        {/* Button kết nối ví chính */}
        <AppKitButton balance="show" />

        {/* Các component tương tác blockchain */}
        <SignMessage />
        <SendTransaction />
        <SignTypedDataV4 />
        <ReadContract />
        <WriteContract />
      </FlexView>

      {/* Modal AppKit */}
      <AppKit />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    rowGap: 16,
  },
  buttonContainer: {
    gap: 8,
  },
  title: {
    marginBottom: 40,
    fontSize: 30,
  },
});

export default App;
```

#### **Cách Lấy WalletConnect Project ID:**

1. Truy cập [WalletConnect Cloud Dashboard](https://cloud.walletconnect.com/)
2. Đăng ký/Đăng nhập tài khoản
3. Tạo project mới
4. Copy Project ID từ dashboard
5. Thay thế `YOUR_WALLETCONNECT_PROJECT_ID` bằng Project ID thực tế

## Kiến Trúc Hệ Thống

### Vấn Đề Cần Giải Quyết và Giải Pháp Blockchain

#### **Vấn Đề Trong Nông Nghiệp**

1. **Thiếu Minh Bạch Trong Chuỗi Cung Ứng**

   - **Vấn đề**: Người tiêu dùng không biết nguồn gốc thực phẩm, quy trình sản xuất
   - **Giải pháp Blockchain**: Ghi lại toàn bộ lịch sử từ gieo trồng → thu hoạch → chế biến → phân phối
   - **Implementation**: Smart contract lưu trữ từng bước với timestamp và người thực hiện

2. **Gian Lận Thông Tin Sản Phẩm**

   - **Vấn đề**: Dễ dàng làm giả chứng nhận organic, VietGAP, xuất xứ
   - **Giải pháp Blockchain**: Dữ liệu bất biến (immutable), không thể chỉnh sửa sau khi ghi
   - **Implementation**: Hash verification, digital signatures từ các cơ quan chứng nhận

3. **Khó Truy Xuất Khi Có Sự Cố**

   - **Vấn đề**: Khi có contamination, khó xác định nguồn gốc và phạm vi ảnh hưởng
   - **Giải pháp Blockchain**: Truy xuất ngược (backward tracing) và xuôi (forward tracing) tức thì
   - **Implementation**: Graph database structure trên blockchain, batch tracking

4. **Thiếu Tin Cậy Giữa Các Bên**
   - **Vấn đề**: Nông dân, nhà phân phối, nhà bán lẻ không tin tưởng thông tin của nhau
   - **Giải pháp Blockchain**: Decentralized trust, không cần trung gian
   - **Implementation**: Multi-signature contracts, consensus mechanism

#### **Áp Dụng Cho Các Lĩnh Vực Khác**

**Bất Động Sản:**

- **Vấn đề**: Gian lận sổ đỏ, tranh chấp quyền sở hữu
- **Giải pháp**: Tokenization tài sản, smart contract cho giao dịch tự động

**Y Tế:**

- **Vấn đề**: Thuốc giả, thiếu minh bạch trong chuỗi cung ứng dược phẩm
- **Giải pháp**: Track từ sản xuất → phân phối → bệnh viện → bệnh nhân

**Logistics:**

- **Vấn đề**: Mất hàng, thông tin vận chuyển không chính xác
- **Giải pháp**: Real-time tracking, IoT integration với blockchain

### Cấu Trúc File

```
src/
├── services/
│   ├── blockchain.js          # Tương tác smart contract
│   ├── wallet.js             # API quản lý ví
│   └── ABI/                  # Contract ABI files
│       ├── farmABI.json
│       └── farm-product.json
├── screens/
│   ├── WalletToken/          # Màn hình quản lý ví
│   ├── Profile/              # Xác thực blockchain
│   └── SupplyChainSteps/     # Cập nhật trạng thái sản phẩm
└── hooks/                    # Custom hooks cho blockchain
```

### Kiến Trúc Giải Quyết Vấn Đề Nông Nghiệp

#### **Mapping Vấn Đề → Smart Contract Functions**

```
Vấn Đề Thực Tế                    Smart Contract Function              Kết Quả
─────────────────────────────────────────────────────────────────────────────────
Đăng ký trang trại               registerCompany()                   → Company ID on-chain
Tạo sản phẩm mới                 createProduct()                     → Product với batch tracking
Cập nhật quy trình chăm sóc      updateProductProcesses()            → Immutable care history
Chứng nhận chất lượng            addCertification()                  → Digital certificates
Thu hoạch sản phẩm               updateProductStatus("HARVESTED")   → Harvest timestamp
Chế biến/đóng gói                updateProductStatus("PROCESSED")   → Processing details
Vận chuyển                       updateProductStatus("SHIPPED")     → Logistics tracking
Bán lẻ                          updateProductStatus("RETAIL")      → Final destination
Truy xuất sự cố                  getProductHistory()                 → Complete audit trail
```

#### **Luồng Dữ Liệu Giải Quyết Tin Cậy**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   NÔNG DÂN      │    │  NHÀ CHỨNG NHẬN │    │  NHÀ PHÂN PHỐI  │    │  NGƯỜI TIÊU DÙNG│
│                 │    │                 │    │                 │    │                 │
│ - Gieo trồng    │    │ - Kiểm tra      │    │ - Vận chuyển    │    │ - Quét QR       │
│ - Chăm sóc      │    │ - Chứng nhận    │    │ - Lưu kho       │    │ - Xem lịch sử   │
│ - Thu hoạch     │    │ - Ký số         │    │ - Phân phối     │    │ - Tin tưởng     │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │                      │
          ▼                      ▼                      ▼                      ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           PIONE BLOCKCHAIN                                          │
│                                                                                     │
│  Smart Contract: Farm Management                                                    │
│  ├── registerCompany(businessID, name, wallet)                                      │
│  ├── createProduct(productId, name, data)                                           │
│  ├── updateProductStatus(productId, batch, status)                                  │
│  ├── addCertification(productId, certType, certData)                                 │
│  └── getProductHistory(productId, batch) → Immutable History                        │
│                                                                                     │
│  Đặc tính Blockchain:                                                               │
│  ✓ Immutable (không thể sửa đổi)                                                    │
│  ✓ Transparent (minh bạch)                                                          │
│  ✓ Decentralized (phi tập trung)                                                    │
│  ✓ Timestamped (có dấu thời gian)                                                   │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

### Luồng Hoạt Động Frontend → PioneWallet → Blockchain → Backend

```
┌─────────────────┐                    ┌─────────────────┐                    ┌─────────────────┐
│                 │                    │                 │                    │                 │
│   FRONTEND      │                    │  PIONE WALLET   │                    │   BLOCKCHAIN    │
│  (React Native) │                    │   (Ví App)      │                    │ (Smart Contract)│
│                 │                    │                 │                    │                 │
└─────────┬───────┘                    └─────────┬───────┘                    └─────────┬───────┘
          │                                      │                                      │
          │ 1. User Action                       │                                      │
          │ (Đăng ký công ty)                    │                                      │
          ▼                                      │                                      │
┌─────────────────┐                              │                                      │
│  Validate Input │                              │                                      │
│  & Trigger      │                              │                                      │
│  Wallet Connect │                              │                                      │
└─────────┬───────┘                              │                                      │
          │                                      │                                      │
          │ 2. WalletConnect Request             │                                      │
          │ (Deep Link to PioneWallet)           │                                      │
          ▼                                      ▼                                      │
┌─────────────────┐                    ┌─────────────────┐                              │
│  Mở PioneWallet │◀───────────────────│  Nhận WC Request│                              │
│  App để kết nối │                    │  & Hiển thị     │                              │
│                 │                    │  Thông tin TX   │                              │
└─────────┬───────┘                    └─────────┬───────┘                              │
          │                                      │                                      │
          │ 3. User xác nhận trong PioneWallet   │                                      │
          │                                      ▼                                      │
          │                            ┌─────────────────┐                              │
          │                            │  User Approve   │                              │
          │                            │  Transaction    │                              │
          │                            │  trong Wallet   │                              │
          │                            └─────────┬───────┘                              │
          │                                      │                                      │
          │                                      │ 4. Ký giao dịch                      │
          │                                      │ với Private Key                      │
          │                                      ▼                                      ▼
          │                            ┌─────────────────┐                    ┌─────────────────┐
          │                            │  Tạo Signed TX  │                    │ Thực thi        │
          │                            │  & Gửi lên      │───────────────────▶│ Smart Contract  │
          │                            │  Blockchain     │                    │ Function        │
          │                            └─────────┬───────┘                    └─────────┬───────┘
          │                                      │                                      │
          │                                      │ 5. TX Hash Response                  │
          │                                      ▼                                      ▼
          │                            ┌─────────────────┐                    ┌─────────────────┐
          │◀───────────────────────────│  Trả TX Hash    │◀───────────────────│  Ghi dữ liệu    │
          │ 6. Nhận TX Hash            │  về Frontend    │                    │  lên Blockchain │
          │                            └─────────────────┘                    └─────────────────┘
          │
          │ 7. Gửi TX Hash lên Backend
          ▼
┌─────────────────┐     ┌─────────────────┐
│  API Call       │     │    BACKEND      │
│  POST /save-tx  │────▶│   (API Server)  │
│  {txHash}       │     │                 │
└─────────┬───────┘     └─────────┬───────┘
          │                       │
          │ 8. Response           │
          │ {success}             │
          ▼                       ▼
┌─────────────────┐     ┌─────────────────┐
│  Cập nhật UI    │◀────│  Lưu TX Hash    │
│  Success State  │     │  vào Database   │
│  & Show TX Hash │     │                 │
└─────────────────┘     └─────────────────┘
          │
          │ 6. Track Transaction
          │ (Optional)
          ▼
┌─────────────────┐
│  Hiển thị TX    │
│  Hash & Link    │
│  đến Explorer   │
└─────────────────┘
```

#### **Chi Tiết Từng Bước (Kiến Trúc Thực Tế):**

1. **Frontend (React Native)**:

   - User thực hiện action (đăng ký công ty, tạo sản phẩm, cập nhật trạng thái, etc.)
   - Xác thực dữ liệu đầu vào, kiểm tra kết nối ví, sau đó tạo Provider và Signer từ ví của người dùng.
   - **Trực tiếp tạo ethers Provider & Signer từ ví đã kết nối**
   - **Gọi smart contract function trực tiếp từ frontend**
   - **Vai trò**: Chịu trách nhiệm tương tác với người dùng, kết nối ví, gọi hàm smart contract, và hiển thị kết quả

2. **Blockchain (Smart Contract)**:

   - Thực thi chức năng của contract trên mạng Pione Zero Chain
   - Validate business logic và các điều kiện của hàm
   - Ghi dữ liệu lên blockchain (immutable data)
   - **Trả về transaction hash (mã giao dịch) cho Frontend**
   - **Vai trò**: Xử lý logic nghiệp vụ, lưu trữ dữ liệu bất biến, đảm bảo tính minh bạch và toàn vẹn của dữ liệu

3. **Frontend → Backend Flow**:
   - Frontend nhận transaction hash từ blockchain sau khi giao dịch hoàn tất
   - Gửi API call để lưu transaction hash lên backend (POST /api/transactions)
   - Backend lưu hash vào database để tracking, audit và quản lý
   - Trả về response success cho Frontend
   - **Vai trò**: Backend KHÔNG thực hiện giao dịch blockchain thay cho frontend, chỉ lưu trữ thông tin giao dịch để tra cứu và quản lý

#### **Lưu Ý Quan Trọng về Kiến Trúc:**

- **Frontend → Blockchain (Trực tiếp)**: Frontend gọi trực tiếp đến smart contract, KHÔNG thông qua backend
- **Frontend → Backend (Chỉ lưu thông tin)**: Backend chỉ nhận và lưu trữ thông tin giao dịch sau khi đã hoàn tất

#### **Lý Do Kiến Trúc Này:**

- **Bảo mật**: Private key không bao giờ rời khỏi ví của user, không được gửi đến backend
- **Decentralized**: Không phụ thuộc vào backend để thực hiện giao dịch, tuân thủ nguyên tắc phi tập trung của blockchain
- **Real-time**: Phản hồi nhanh từ blockchain trực tiếp, giảm độ trễ và điểm lỗi tiềm ẩn
- **Backup**: Backend chỉ lưu trữ hash để tracking và audit, không can thiệp vào quá trình giao dịch

## ABI (Application Binary Interface)

ABI là một file JSON mô tả các hàm và sự kiện của Smart Contract, giúp Ethers.js biết cách tương tác với nó. File này được tạo ra sau khi bạn biên dịch (compile) code Solidity của Smart Contract.

### Khái Niệm ABI

```javascript
// Ví dụ về một file ABI (farm-product.json)
[
  {
    inputs: [
      {internalType: 'uint256', name: '_productId', type: 'uint256'},
      {internalType: 'string', name: '_batch', type: 'string'},
    ],
    name: 'getProductByBatch',
    outputs: [
      {
        components: [
          {internalType: 'string', name: 'name', type: 'string'},
          {internalType: 'string', name: 'status', type: 'string'},
          {internalType: 'uint256', name: 'timestamp', type: 'uint256'},
        ],
        internalType: 'struct FarmProduct.Product',
        name: '',
        type: 'tuple',
      },
    ],
    stateMutability: 'view',
    type: 'function',
  },
];
```

### Vai Trò Của ABI

- **Định Nghĩa Giao Diện**: ABI định nghĩa các hàm, sự kiện và cấu trúc dữ liệu của smart contract
- **Mã Hóa/Giải Mã**: Cho phép ứng dụng mã hóa đúng tham số gọi hàm và giải mã kết quả trả về
- **Tương Thích**: Đảm bảo ứng dụng có thể giao tiếp với smart contract đúng cách

### Cách Lấy ABI

1. **Từ Blockchain Explorer**: Tải ABI từ ZeroScan khi contract đã được verify

   - Truy cập ZeroScan.org
   - Tìm contract address
   - Vào tab "Contract" -> Code -> và kéo xuống "Contract ABI" và copy ABI

### Quản Lý Địa Chỉ Contract Động

Trong ứng dụng thực tế, địa chỉ contract thường thay đổi theo môi trường hoặc được tạo động. Dưới đây là các phương pháp quản lý địa chỉ contract hiệu quả:

#### 1. Sử Dụng Contract Registry

```javascript
// src/config/contracts.js
export const CONTRACTS = {
  development: {
    FARM_MAIN: '0xabc123...',
    FARM_PRODUCT: '0xdef456...',
  },
  production: {
    FARM_MAIN: '0x789xyz...',
    FARM_PRODUCT: '0x321uvw...',
  },
};
```

## Tương Tác Với Smart Contract

### 1. Cấu Hình Contract (src/services/blockchain.js)

```javascript
import {BrowserProvider, Contract, Interface} from 'ethers';
import farmProductABI from './ABI/farm-product.json';
import farmABI from './ABI/farmABI.json';

// Địa chỉ contract trên Pione Zero Chain
const contractAddress = '0xfD3417DD354...D7cAd63E4313cb1';

/**
 * Tạo instance của contract chính (Farm Management Contract)
 * @param {Signer} signer - Ethers signer để ký giao dịch
 * @returns {Contract} Contract instance cho các thao tác quản lý farm
 */
const getContract = signer => {
  return new Contract(contractAddress, farmABI, signer);
};

/**
 * Tạo instance của contract sản phẩm nông nghiệp
 * @param {string} contractAgri - Địa chễ contract sản phẩm cụ thể
 * @param {Signer} signer - Ethers signer để ký giao dịch
 * @returns {Contract} Contract instance cho các thao tác với sản phẩm
 */
const getContractFarmProduct = (contractAgri, signer) => {
  return new Contract(contractAgri, farmProductABI, signer);
};
```

### 2. Kết Nối Ví và Lấy Provider

```javascript
import {
  useAppKitProvider,
  useAppKitAccount,
} from '@reown/appkit-ethers-react-native';

/**
 * Hook để lấy thông tin ví và provider trong React component
 */
const WalletComponent = () => {
  // Lấy wallet provider từ Reown AppKit
  const walletProvider = useAppKitProvider();

  // Lấy thông tin tài khoản hiện tại
  const {address, isConnected} = useAppKitAccount();

  /**
   * Tạo ethers provider và signer từ wallet provider
   * Provider: để đọc dữ liệu từ blockchain
   * Signer: để ký và gửi giao dịch
   */
  const setupEthersProvider = async () => {
    if (!walletProvider || !isConnected) {
      throw new Error('Ví chưa được kết nối');
    }

    const ethersProvider = new BrowserProvider(walletProvider);
    const signer = await ethersProvider.getSigner();

    return {ethersProvider, signer};
  };
};
```

### 3. Đọc Dữ Liệu Từ Contract (Read Operations)

```javascript
/**
 * Lấy thông tin công ty từ blockchain
 * @param {Object} params - Tham số đầu vào
 * @param {Object} params.walletProvider - Wallet provider từ Reown AppKit
 * @param {string} params.businessID - Mã đăng ký kinh doanh của công ty
 * @returns {Promise<Object>} Thông tin công ty (tên, địa chỉ, thông tin liên hệ)
 * @throws {Error} Nếu có lỗi trong quá trình đọc dữ liệu
 */
export const getCompanyInfo = async ({walletProvider, businessID}) => {
  try {
    // Tạo ethers provider từ wallet provider
    const ethersProvider = new BrowserProvider(walletProvider);

    // Lấy contract instance (không cần signer cho read operations)
    const contract = getContract(ethersProvider);

    // Gọi hàm getCompanyInfo trên smart contract
    const info = await contract.getCompanyInfo(String(businessID));
    return info;
  } catch (error) {
    console.error('Lỗi khi lấy thông tin công ty:', error);
    throw error;
  }
};

/**
 * Lấy thông tin sản phẩm mới nhất theo batch từ blockchain
 * @param {Object} params - Tham số đầu vào
 * @param {Object} params.walletProvider - Wallet provider từ Reown AppKit
 * @param {string} params.productId - ID của sản phẩm
 * @param {string} params.batch - Mã lô sản phẩm
 * @param {string} params.contractAgri - Địa chỉ contract sản phẩm nông nghiệp
 * @returns {Promise<Object>} Thông tin sản phẩm đã được format
 * @throws {Error} Nếu có lỗi trong quá trình đọc dữ liệu
 */
export const fetchProductByBatch = async ({
  walletProvider,
  productId,
  batch,
  contractAgri,
}) => {
  try {
    // Tạo ethers provider từ wallet provider
    const ethersProvider = new BrowserProvider(walletProvider);

    // Lấy contract instance của sản phẩm nông nghiệp
    const contract = getContractFarmProduct(contractAgri, ethersProvider);

    // Gọi hàm getProductByBatch trên smart contract
    const info = await contract.getProductByBatch(
      String(productId),
      String(batch),
    );

    // Format dữ liệu trả về từ contract thành object dễ sử dụng
    return {
      id: info[0], // ID sản phẩm
      productName: info[1], // Tên sản phẩm
      productData: JSON.parse(info[2]), // Dữ liệu sản phẩm (JSON)
      status: info[3], // Trạng thái hiện tại
      careActivityData: JSON.parse(info[4] || 'null'), // Dữ liệu chăm sóc (JSON)
    };
  } catch (error) {
    console.error('Lỗi khi lấy thông tin sản phẩm:', error);
    throw error;
  }
};
```

### 4. Ghi Dữ Liệu Lên Contract (Write Operations)

```javascript
/**
 * Đăng ký công ty mới trên blockchain
 * @param {Object} params - Tham số đầu vào
 * @param {string} params.businessID - Mã đăng ký kinh doanh của công ty
 * @param {string} params.name - Tên công ty
 * @param {string} params.wallet - Địa chỉ ví của công ty
 * @param {Object} params.walletProvider - Wallet provider từ Reown AppKit
 * @returns {Promise<string>} Transaction hash của giao dịch đăng ký
 * @throws {Error} Nếu có lỗi trong quá trình đăng ký
 */
export const registerCompany = async ({
  businessID,
  name,
  wallet,
  walletProvider,
}) => {
  try {
    // Kiểm tra wallet provider có tồn tại
    if (!walletProvider) {
      throw new Error(
        'Không tìm thấy ví blockchain. Vui lòng kết nối ví trước.',
      );
    }

    // Tạo ethers provider và signer (cần signer cho write operations)
    const ethersProvider = new BrowserProvider(walletProvider);
    const signer = await ethersProvider.getSigner();

    // Lấy contract instance với signer
    const contract = getContract(signer);

    // Thực hiện giao dịch đăng ký công ty
    const tx = await contract.registerCompany(String(businessID), name, wallet);

    // Đợi cho đến khi giao dịch được xác nhận trên blockchain
    const receipt = await tx.wait();

    console.log('Đăng ký công ty thành công:', receipt);
    return receipt.hash; // Trả về transaction hash
  } catch (error) {
    console.error('Lỗi khi đăng ký công ty:', error);
    throw error;
  }
};

/**
 * Tạo sản phẩm nông nghiệp mới trên blockchain
 * @param {Object} params - Tham số đầu vào
 * @param {string} params.productId - ID duy nhất của sản phẩm
 * @param {string} params.productName - Tên sản phẩm
 * @param {string|Object} params.productData - Dữ liệu sản phẩm (JSON string hoặc object)
 * @param {Object} params.walletProvider - Wallet provider từ Reown AppKit
 * @param {string} params.contractAgri - Địa chỉ contract sản phẩm nông nghiệp
 * @returns {Promise<string>} Transaction hash của giao dịch tạo sản phẩm
 * @throws {Error} Nếu có lỗi trong quá trình tạo sản phẩm
 */
export const createFarmProduct = async ({
  productId,
  productName,
  productData,
  walletProvider,
  contractAgri,
}) => {
  try {
    // Tạo ethers provider và signer
    const ethersProvider = new BrowserProvider(walletProvider);
    const signer = await ethersProvider.getSigner();

    // Lấy contract instance của sản phẩm nông nghiệp
    const contract = getContractFarmProduct(contractAgri, signer);

    // Chuẩn bị dữ liệu sản phẩm (chuyển thành JSON string nếu cần)
    const formattedProductData =
      typeof productData === 'string'
        ? productData
        : JSON.stringify(productData);

    // Thực hiện giao dịch tạo sản phẩm
    const tx = await contract.createProduct(
      String(productId),
      productName,
      formattedProductData,
    );

    // Đợi cho đến khi giao dịch được xác nhận trên blockchain
    const receipt = await tx.wait();

    return receipt.hash; // Trả về transaction hash
  } catch (error) {
    console.error('Lỗi khi tạo sản phẩm:', error);
    throw error;
  }
};

/**
 * Cập nhật trạng thái sản phẩm trên blockchain
 * @param {Object} params - Tham số đầu vào
 * @param {string} params.productId - ID của sản phẩm
 * @param {string} params.batch - Mã lô sản phẩm
 * @param {string} params.status - Trạng thái mới (VD: 'PLANTED', 'HARVESTED', 'PROCESSED')
 * @param {Object} params.walletProvider - Wallet provider từ Reown AppKit
 * @param {string} params.contractAgri - Địa chỉ contract sản phẩm nông nghiệp
 * @returns {Promise<string>} Transaction hash của giao dịch cập nhật
 * @throws {Error} Nếu có lỗi trong quá trình cập nhật
 */
export const updateProductStatus = async ({
  productId,
  batch,
  status,
  walletProvider,
  contractAgri,
}) => {
  try {
    // Tạo ethers provider và signer
    const ethersProvider = new BrowserProvider(walletProvider);
    const signer = await ethersProvider.getSigner();

    // Lấy contract instance của sản phẩm nông nghiệp
    const contract = getContractFarmProduct(contractAgri, signer);

    // Thực hiện giao dịch cập nhật trạng thái
    const tx = await contract.updateProductStatus(
      String(productId),
      String(batch),
      String(status),
    );

    // Đợi cho đến khi giao dịch được xác nhận trên blockchain
    const receipt = await tx.wait();

    return receipt.hash; // Trả về transaction hash
  } catch (error) {
    console.error('Lỗi khi cập nhật trạng thái sản phẩm:', error);
    throw error;
  }
};
```

## Quản Lý Ví (Wallet Management)

### 1. Kết Nối Ví Với Reown

```javascript
import {
  useAppKit,
  useAppKitAccount,
  useAppKitProvider,
  useDisconnect,
} from '@reown/appkit-ethers-react-native';
import {BrowserProvider} from 'ethers';

// Hook để quản lý kết nối ví
export const useWalletConnection = () => {
  const {open, close} = useAppKit();
  const {address, isConnected, caipAddress, status} = useAppKitAccount();
  const {disconnect} = useDisconnect();
  const walletProvider = useAppKitProvider();

  // Kết nối ví
  const connectWallet = async () => {
    try {
      await open();
    } catch (error) {
      console.error('Lỗi khi kết nối ví:', error);
      throw error;
    }
  };

  // Ngắt kết nối ví
  const disconnectWallet = async () => {
    try {
      await disconnect();
    } catch (error) {
      console.error('Lỗi khi ngắt kết nối ví:', error);
      throw error;
    }
  };

  // Lấy balance của ví
  const getWalletBalance = async () => {
    if (!walletProvider || !address) return null;

    try {
      const ethersProvider = new BrowserProvider(walletProvider);
      const balance = await ethersProvider.getBalance(address);
      return balance;
    } catch (error) {
      console.error('Lỗi khi lấy balance:', error);
      throw error;
    }
  };

  return {
    address,
    isConnected,
    caipAddress,
    status,
    walletProvider,
    connectWallet,
    disconnectWallet,
    getWalletBalance,
  };
};
```

### 2. Sử Dụng Component Của Reown để Kết Nối Ví

```javascript
import {
  AppKitButton,
  useAppKitAccount,
} from '@reown/appkit-ethers-react-native';

const WalletComponent = () => {
  const {address, isConnected} = useAppKitAccount();

  return (
    <View>
      {/* Component button có sẵn của Reown */}
      <AppKitButton />

      {/* Hiển thị thông tin kết nối */}
      {isConnected && (
        <Text style={{marginTop: 10}}>Đã kết nối: {address}</Text>
      )}
    </View>
  );
};
```

### 3. Các Component Khác Của Reown

```javascript
import {
  AppKitButton,
  AppKitNetworkButton,
  useAppKitAccount,
  useAppKitNetwork,
} from '@reown/appkit-ethers-react-native';

const AdvancedWalletComponent = () => {
  const {address, isConnected} = useAppKitAccount();
  const {chainId} = useAppKitNetwork();

  return (
    <View style={{padding: 20}}>
      {/* Button kết nối ví */}
      <AppKitButton />

      {/* Button chuyển đổi network */}
      <AppKitNetworkButton style={{marginTop: 10}} />

      {/* Thông tin chi tiết */}
      {isConnected && (
        <View style={{marginTop: 15}}>
          <Text>Địa chỉ ví: {address}</Text>
          <Text>Chain ID: {chainId}</Text>
        </View>
      )}
    </View>
  );
};
```

## Quản Lý State và Giao Dịch

### 1. Theo Dõi Lịch Sử Giao Dịch trên Pione Zero Chain

```javascript
export const fetchHistoryFarmProduct = async ({contractAgri}) => {
  try {
    // Lấy lịch sử giao dịch từ ZeroScan API
    const response = await axios.get(
      `https://zeroscan.org/api?&module=account&action=txlist&address=${contractAgri}&startblock=0&endblock=99999999&page=1&offset=20&sort=desc`,
    );

    const rawResponse = response.data.result;
    const iface = new Interface(farmProductABI);

    const processedResult = await Promise.all(
      (rawResponse || []).map(async tx => {
        if (!tx.functionName.includes('updateProductProcesses')) return null;

        const decoded = iface.decodeFunctionData(
          'updateProductProcesses',
          tx.input,
        );

        return {
          hash: tx.hash,
          productId: decoded._productId,
          batch: decoded.batch,
          ...JSON.parse(decoded._newProcesses || '{}'),
          timestamp: tx.timeStamp,
          date: new Date(tx.timeStamp * 1000).toISOString(),
        };
      }),
    );

    return processedResult.filter(item => Boolean(item));
  } catch (error) {
    console.error('Lỗi khi lấy lịch sử:', error);
    throw error;
  }
};
```

### 2. Custom Hook Cho Blockchain Operations

```javascript
// src/screens/SupplyChainSteps/hooks/useBlockchainUpdateStatusWithCheck.js
import {useAppKitProvider} from '@reown/appkit-ethers-react-native';
import {updateProductStatus} from '~/services/blockchain';

export const useBlockchainUpdateStatusWithCheck = () => {
  const walletProvider = useAppKitProvider();

  const updateStatus = async ({productId, batch, status, contractAgri}) => {
    try {
      const txHash = await updateProductStatus({
        productId,
        batch,
        status,
        walletProvider,
        contractAgri,
      });

      return txHash;
    } catch (error) {
      throw error;
    }
  };

  return {updateStatus};
};
```

### 3. Quản Lý State Với React Query

```javascript
// src/screens/ProductDetails/hooks/useProductData.js
import {useQuery, useMutation, useQueryClient} from '@tanstack/react-query';
import {fetchProductByBatch, updateProductStatus} from '~/services/blockchain';
import {useAppKitProvider} from '@reown/appkit-ethers-react-native';

// Hook để quản lý state và cache dữ liệu blockchain
export const useProductData = (productId, batch, contractAgri) => {
  const walletProvider = useAppKitProvider();
  const queryClient = useQueryClient();

  // Lấy thông tin mới nhất từ blockchain (chỉ lấy batch hiện tại)
  // Lưu ý: Chỉ lấy dữ liệu mới nhất từ blockchain để tối ưu gas fee

  // Còn danh sách lịch sử canh tác sẽ được lấy từ backend API để tăng hiệu suất
  const {
    data: product,
    isLoading,
    error,
    refetch,
  } = useQuery({
    queryKey: ['product', productId, batch, contractAgri],
    queryFn: () =>
      fetchProductByBatch({
        walletProvider,
        productId,
        batch,
        contractAgri,
      }),
    staleTime: 60000, // Dữ liệu được coi là "cũ" sau 1 phút
    cacheTime: 300000, // Cache dữ liệu trong 5 phút
  });

  // Mutation để cập nhật trạng thái sản phẩm
  const updateMutation = useMutation({
    mutationFn: newStatus =>
      updateProductStatus({
        productId,
        batch,
        status: newStatus,
        walletProvider,
        contractAgri,
      }),
    onSuccess: txHash => {
      // Invalidate và refetch sau khi cập nhật thành công
      queryClient.invalidateQueries([
        'product',
        productId,
        batch,
        contractAgri,
      ]);
      return txHash;
    },
  });

  return {
    product,
    isLoading,
    error,
    refetch,
    updateProduct: updateMutation.mutate,
    isUpdating: updateMutation.isLoading,
    updateError: updateMutation.error,
    txHash: updateMutation.data,
  };
};
```

#### **Lợi Ích Của React Query Trong Web3:**

- **Caching**: Giảm số lượng RPC calls đến blockchain
- **Auto-refetching**: Tự động cập nhật dữ liệu khi cần
- **Invalidation**: Quản lý cache thông minh sau khi thực hiện giao dịch
- **Loading & Error States**: Quản lý trạng thái loading và error dễ dàng
- **Optimistic Updates**: Cập nhật UI ngay lập tức trước khi blockchain xác nhận

#### **Sử Dụng Trong Component:**

```javascript
import React, {useState} from 'react';
import {View, Text, Button, Alert, ScrollView, StyleSheet} from 'react-native';
import {useProductData} from '../hooks/useProductData';
import {useAppKitAccount} from '@reown/appkit-ethers-react-native';

const ProductDetailScreen = ({route}) => {
  const {productId, batch, contractAgri} = route.params;
  const {product, isLoading, error, updateProduct, isUpdating, txHash} =
    useProductData(productId, batch, contractAgri);
  const {isConnected, address} = useAppKitAccount();
  const [selectedStatus, setSelectedStatus] = useState('');

  // Các trạng thái có thể cập nhật
  const statusOptions = [
    {value: 'PLANTED', label: 'Đã trồng', color: '#4CAF50'},
    {value: 'GROWING', label: 'Đang phát triển', color: '#FF9800'},
    {value: 'HARVESTED', label: 'Đã thu hoạch', color: '#2196F3'},
    {value: 'PROCESSED', label: 'Đã xử lý', color: '#9C27B0'},
    {value: 'PACKAGED', label: 'Đã đóng gói', color: '#607D8B'},
    {value: 'SHIPPED', label: 'Đã vận chuyển', color: '#795548'},
  ];

  const handleUpdateStatus = async newStatus => {
    if (!isConnected) {
      Alert.alert('Lỗi', 'Vui lòng kết nối ví trước khi cập nhật');
      return;
    }

    try {
      await updateProduct(newStatus);
      Alert.alert(
        'Thành công',
        `Cập nhật trạng thái thành công!\nTrạng thái mới: ${newStatus}`,
        [{text: 'OK'}],
      );
    } catch (error) {
      Alert.alert('Lỗi', error.message || 'Không thể cập nhật trạng thái');
    }
  };

  const getStatusColor = status => {
    const statusOption = statusOptions.find(option => option.value === status);
    return statusOption ? statusOption.color : '#757575';
  };

  const getStatusLabel = status => {
    const statusOption = statusOptions.find(option => option.value === status);
    return statusOption ? statusOption.label : status;
  };

  if (isLoading) {
    return (
      <View style={styles.centerContainer}>
        <Text>Loading...</Text>
      </View>
    );
  }

  if (error) {
    return (
      <View style={styles.centerContainer}>
        <Text style={styles.errorText}>Lỗi: {error.message}</Text>
        <Button title="Thử lại" onPress={() => window.location.reload()} />
      </View>
    );
  }

  return (
    <ScrollView style={styles.container}>
      {/* Thông tin sản phẩm */}
      <View style={styles.productInfo}>
        <Text style={styles.title}>Thông Tin Sản Phẩm</Text>
        <Text style={styles.label}>
          ID Sản phẩm: <Text style={styles.value}>{productId}</Text>
        </Text>
        <Text style={styles.label}>
          Tên sản phẩm:{' '}
          <Text style={styles.value}>{product?.productName || 'Chưa có'}</Text>
        </Text>
        <Text style={styles.label}>
          Batch: <Text style={styles.value}>{batch}</Text>
        </Text>
        <Text style={styles.label}>
          Trạng thái hiện tại:
          <Text
            style={[
              styles.statusBadge,
              {backgroundColor: getStatusColor(product?.status)},
            ]}>
            {getStatusLabel(product?.status)}
          </Text>
        </Text>
      </View>

      {/* Thông tin chi tiết */}
      {product?.productData && (
        <View style={styles.detailInfo}>
          <Text style={styles.title}>Chi Tiết Sản Phẩm</Text>
          <Text style={styles.label}>
            Giống:{' '}
            <Text style={styles.value}>
              {product.productData.variety || 'Chưa có'}
            </Text>
          </Text>
          <Text style={styles.label}>
            Ngày trồng:{' '}
            <Text style={styles.value}>
              {product.productData.plantDate || 'Chưa có'}
            </Text>
          </Text>
          <Text style={styles.label}>
            Vị trí:{' '}
            <Text style={styles.value}>
              {product.productData.location || 'Chưa có'}
            </Text>
          </Text>
          <Text style={styles.label}>
            Diện tích:{' '}
            <Text style={styles.value}>
              {product.productData.area || 'Chưa có'}
            </Text>
          </Text>
        </View>
      )}

      {/* Thông tin chăm sóc */}
      {product?.careActivityData && (
        <View style={styles.careInfo}>
          <Text style={styles.title}>Thông Tin Chăm Sóc</Text>
          <Text style={styles.label}>
            Lần tưới cuối:{' '}
            <Text style={styles.value}>
              {product.careActivityData.lastWatering || 'Chưa có'}
            </Text>
          </Text>
          <Text style={styles.label}>
            Phân bón:{' '}
            <Text style={styles.value}>
              {product.careActivityData.fertilizer || 'Chưa có'}
            </Text>
          </Text>
          <Text style={styles.label}>
            Thuốc trừ sâu:{' '}
            <Text style={styles.value}>
              {product.careActivityData.pesticide || 'Chưa có'}
            </Text>
          </Text>
        </View>
      )}

      {/* Cập nhật trạng thái */}
      <View style={styles.updateSection}>
        <Text style={styles.title}>Cập Nhật Trạng Thái</Text>

        {!isConnected ? (
          <Text style={styles.warningText}>
            Vui lòng kết nối ví để cập nhật trạng thái
          </Text>
        ) : (
          <View style={styles.statusButtons}>
            {statusOptions.map(option => (
              <Button
                key={option.value}
                title={option.label}
                onPress={() => handleUpdateStatus(option.value)}
                disabled={isUpdating || product?.status === option.value}
                color={option.color}
              />
            ))}
          </View>
        )}
      </View>

      {/* Thông tin giao dịch */}
      {txHash && (
        <View style={styles.transactionInfo}>
          <Text style={styles.title}>Giao Dịch Gần Nhất</Text>
          <Text style={styles.label}>Transaction Hash:</Text>
          <Text style={styles.txHash}>{txHash}</Text>
          <Text style={styles.explorerLink}>Xem trên ZeroScan Explorer</Text>
        </View>
      )}

      {/* Trạng thái loading */}
      {isUpdating && (
        <View style={styles.loadingOverlay}>
          <Text style={styles.loadingText}>Đang cập nhật trạng thái...</Text>
        </View>
      )}
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
    padding: 16,
  },
  centerContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  productInfo: {
    backgroundColor: 'white',
    padding: 16,
    borderRadius: 8,
    marginBottom: 16,
    shadowColor: '#000',
    shadowOffset: {width: 0, height: 2},
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 3,
  },
  detailInfo: {
    backgroundColor: 'white',
    padding: 16,
    borderRadius: 8,
    marginBottom: 16,
  },
  careInfo: {
    backgroundColor: 'white',
    padding: 16,
    borderRadius: 8,
    marginBottom: 16,
  },
  updateSection: {
    backgroundColor: 'white',
    padding: 16,
    borderRadius: 8,
    marginBottom: 16,
  },
  transactionInfo: {
    backgroundColor: 'white',
    padding: 16,
    borderRadius: 8,
    marginBottom: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 12,
    color: '#333',
  },
  label: {
    fontSize: 14,
    color: '#666',
    marginBottom: 8,
  },
  value: {
    fontWeight: '600',
    color: '#333',
  },
  statusBadge: {
    color: 'white',
    paddingHorizontal: 8,
    paddingVertical: 4,
    borderRadius: 4,
    fontSize: 12,
    fontWeight: 'bold',
  },
  statusButtons: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    gap: 8,
  },
  warningText: {
    color: '#FF9800',
    textAlign: 'center',
    fontStyle: 'italic',
  },
  errorText: {
    color: '#F44336',
    textAlign: 'center',
    marginBottom: 16,
  },
  txHash: {
    fontFamily: 'monospace',
    fontSize: 12,
    backgroundColor: '#f0f0f0',
    padding: 8,
    borderRadius: 4,
    marginVertical: 4,
  },
  explorerLink: {
    color: '#2196F3',
    textDecorationLine: 'underline',
    fontSize: 12,
  },
  loadingOverlay: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    backgroundColor: 'rgba(0,0,0,0.5)',
    justifyContent: 'center',
    alignItems: 'center',
  },
  loadingText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
});

export default ProductDetailScreen;
```

## Tài Liệu Tham Khảo

### Thư viện và SDK

1. [Ethers.js Documentation](https://docs.ethers.org/v6/) - Tài liệu chính thức của thư viện Ethers.js v6
2. [Reown AppKit Documentation](https://docs.reown.com/appkit/) - Tài liệu về Reown AppKit SDK
3. [WalletConnect Documentation](https://docs.walletconnect.com/) - Tài liệu chính thức của WalletConnect
4. [React Query Documentation](https://tanstack.com/query/latest) - Tài liệu về TanStack Query (React Query)
5. [Axios Documentation](https://axios-http.com/vi/docs/intro) - Tài liệu về Axios HTTP client

### Blockchain và Smart Contract

1. [Pione Zero Chain Explorer](https://zeroscan.org/) - Block explorer chính thức của Pione Zero Chain
2. [Pione Chain Explorer](https://scan.pione.io/) - Block explorer chính thức của Pione Chain mainnet
3. [Solidity Documentation](https://docs.soliditylang.org/) - Tài liệu về ngôn ngữ lập trình Solidity
4. [OpenZeppelin Contracts](https://docs.openzeppelin.com/contracts/) - Thư viện smart contract bảo mật
5. [EVM Opcodes](https://www.evm.codes/) - Tham khảo về EVM opcodes và gas costs

### API và Services

1. [ZeroScan API Documentation](https://zeroscan.org//api-docs) - Tài liệu API của ZeroScan
2. [Gas Fee Concepts](https://ethereum.org/en/developers/docs/gas/) - Kỹ thuật về phí gas cho Ethereum và EVM chains
3. [JSON-RPC API](https://ethereum.org/en/developers/docs/apis/json-rpc/) - Tài liệu về Ethereum JSON-RPC API
4. [Web3 Provider API](https://docs.ethers.org/v6/api/providers/) - Tài liệu về Web3 providers

### React Native và Mobile Development

1. [React Native Documentation](https://reactnative.dev/docs/getting-started) - Tài liệu chính thức React Native
2. [Expo Documentation](https://docs.expo.dev/) - Tài liệu về Expo framework
3. [React Navigation](https://reactnavigation.org/docs/getting-started) - Navigation library cho React Native
4. [AsyncStorage](https://react-native-async-storage.github.io/async-storage/) - Local storage cho React Native

### Tools và Utilities

1. [MetaMask Developer Documentation](https://docs.metamask.io/) - Tài liệu dành cho developers của MetaMask
2. [Hardhat Documentation](https://hardhat.org/docs) - Framework phát triển smart contract
3. [Remix IDE](https://remix.ethereum.org/) - IDE online cho Solidity
4. [ABI Encoder/Decoder](https://abi.hashex.org/) - Tool encode/decode ABI
5. [Unit Converter](https://eth-converter.com/) - Chuyển đổi đơn vị ETH/Wei/Gwei
