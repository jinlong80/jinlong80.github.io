# iptables

## Command

  Best way to reset all iptables rules
  ```bash
  iptables -P INPUT ACCEPT
  iptables -P FORWARD ACCEPT
  iptables -P OUTPUT ACCEPT
  iptables -t nat -F
  iptables -t mangle -F
  iptables -F
  iptables -X

  Clear ip6tables rules:
  ip6tables -P INPUT ACCEPT
  ip6tables -P FORWARD ACCEPT
  ip6tables -P OUTPUT ACCEPT
  ip6tables -t nat -F
  ip6tables -t mangle -F
  ip6tables -F
  ip6tables -X
  ```

## Sample
* Allow in and outbound ip and port

  For MongoDb
  ```bash
  iptables -A INPUT -s 10.10.122.135 -p tcp --destination-port 27017 -m state --state NEW,ESTABLISHED -j ACCEPT
  iptables -A OUTPUT -d 10.10.122.135 -p tcp --source-port 27017 -m state --state ESTABLISHED -j ACCEPT
  ```

  Save as persistence:
  ```bash
  iptables-save > /etc/iptables.conf
  ```

## Reference
[Configure Linux IPTABLES Firewall for MongoDb](https://docs.mongodb.com/manual/tutorial/configure-linux-iptables-firewall/)