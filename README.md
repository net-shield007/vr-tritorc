This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

It's a WebXR virtual cinema for Tritorc product videos, built with Next.js and Three.js. It runs as a normal webpage, and on a Meta Quest (or any WebXR-capable headset) it can launch an immersive VR session with an "Enter VR" button.

## Setup from git clone

```bash
git clone https://github.com/net-shield007/vr-tritorc.git
cd vr-tritorc
npm install
```

Add your video files to `public/videos/` (see the `url` fields in `app/page.js` for the expected filenames).

### Generate a local HTTPS certificate

WebXR only works over a secure origin (HTTPS or `localhost`) — a Quest browser will not expose the VR APIs over plain HTTP. This repo's `npm run dev` script expects a self-signed cert at `certs/localhost-key.pem` / `certs/localhost-cert.pem` (this folder is gitignored, so you generate your own after cloning):

```bash
mkdir -p certs
openssl req -x509 -newkey rsa:2048 -keyout certs/localhost-key.pem -out certs/localhost-cert.pem \
  -days 3650 -nodes -subj "/CN=localhost" \
  -addext "subjectAltName=DNS:localhost,IP:127.0.0.1,IP:YOUR_LAN_IP"
```

Replace `YOUR_LAN_IP` with your machine's LAN IP address (see below for how to find it). On Windows, run this from Git Bash; on macOS/Linux, any shell works.

Then run:

```bash
npm run dev
```

Open [https://localhost:3000](https://localhost:3000) with your browser to see the result (accept the self-signed certificate warning — click **Advanced → Proceed**).

You can start editing the page by modifying `app/page.js`. The page auto-updates as you edit the file.

## Running on your local network (for a VR headset)

To open the app on a Meta Quest or other headset, it needs to reach your computer over Wi-Fi.

1. **Find your machine's LAN IP address.**
   - Windows (PowerShell): `ipconfig` → look for the `IPv4 Address` under your active adapter (e.g. `192.168.1.81`).
   - macOS/Linux: `ifconfig` or `ip addr`.

2. **Make sure that IP is included in the cert's `subjectAltName`** (see the `openssl` command above) — regenerate the cert if you didn't include it originally.

3. **Allow the port through your firewall**, so other devices on the network can connect (Windows only needs this if you get a connection timeout from another device; run as Administrator):

   ```powershell
   New-NetFirewallRule -DisplayName "Node dev server 3000" -Direction Inbound -Protocol TCP -LocalPort 3000 -Action Allow
   ```

4. **Start the dev server** with `npm run dev`. It will print a `Network:` URL, e.g. `https://192.168.1.81:3000`.

5. **Make sure your headset is on the same Wi-Fi network** as your computer (same router/subnet).

6. On the headset's browser, open `https://<your-lan-ip>:3000` — type the full address including `https://`, since typing just `<ip>:3000` without a scheme will default to `http://` and fail with `ERR_EMPTY_RESPONSE`.

7. Accept the certificate warning (**Advanced → Proceed**, or the headset browser's equivalent).

8. Pick a video, then tap **Enter VR** to launch the immersive session.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/app/building-your-application/deploying) for more details.
