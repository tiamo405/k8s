test allow label thì run 1 pod rồi call đến service của pod đó, nếu có response thì chứng tỏ label đã được gán đúng và service đã có thể gọi đến pod đó
```bash
kubectl run test-client \
  --image=busybox \
  -l app=client \
  -n final-exam \
  --rm -it -- sh
wget -qO- http://app-final
```