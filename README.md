# GetUserIP
How to Get the IP Address in JavaScript

## One


### First of all, you have to do
// chrome://flags/#enable-webrtc-hide-local-ips-with-mdns
// edge://flags/#enable-webrtc-hide-local-ips-with-mdns
// set as disabled


### Function
```js

/**
 * Get user IP
 * @param {function} onNewIP
 */
export function getUserIP(onNewIP) {
  //  onNewIp - your listener function for new IPs compatibility for firefox and chrome
  const PeerConnection =
    window.RTCPeerConnection || window.mozRTCPeerConnection || window.webkitRTCPeerConnection;
  const pc = new PeerConnection({
    iceServers: [],
  });
  const noop = () => {};
  const localIPs = {};
  const ipRegex = /([0-9]{1,3}(\.[0-9]{1,3}){3}|[a-f0-9]{1,4}(:[a-f0-9]{1,4}){7})/g;

  const iterateIP = (ip) => {
    if (!localIPs[ip]) {
      if (typeof onNewIP === "function") {
        onNewIP(ip);
      }
    }
    localIPs[ip] = true;
  };

  //create a bogus data channel
  pc.createDataChannel("");

  // create offer and set local description
  pc.createOffer()
    .then((sdp) => {
      sdp.sdp.split("\n").forEach((line) => {
        if (line.indexOf("candidate") < 0) {
          return;
        }
        line.match(ipRegex).forEach(iterateIP);
      });
      pc.setLocalDescription(sdp, noop, noop);
    })
    .catch((err) => {
      // An error occurred, so handle the failure to connect
      console.log(err);
    });

  // sten for candidate events
  pc.onicecandidate = (ice) => {
    if (
      !ice ||
      !ice.candidate ||
      !ice.candidate.candidate ||
      !ice.candidate.candidate.match(ipRegex)
    ) {
      return;
    }
    ice.candidate.candidate.match(ipRegex).forEach(iterateIP);
  };
}
```

### Usage
```js
getUserIP((ip) => {
  console.log("Your IP:" + ip);
});
```

## Two

https://www.abstractapi.com/guides/how-to-get-a-client-ip-address-in-node-js



