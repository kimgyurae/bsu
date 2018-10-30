# bsu

```
#include <eosiolib/eosio.hpp>
#include <eosiolib/print.hpp>
#include <string>

using namespace eosio;
using std::string;

class table : public contract {
public:
  table(account_name self) : contract(self) {} // class constructor                                   //@abi action                                                                                                                     
  void create(const account_name account,
              const string& type,
              const string& userid,
              uint32_t transid,
              uint64_t transtime,
              const string& content,
              uint32_t amount,
              const string& party
              );

  //@abi action                                                                                                                                                                                                                          
  void update(const account_name account,
              const string& type,
              const string& userid,
              uint32_t transid,
              uint64_t transtime,
              const string& content,
              uint32_t amount,
              const string& party
              );

  void remove(const account_name account);
private:
  //@abi table profiles i64                                                                                                                                                                                                              
  struct profilef {
    account_name account;
    string type;
    string userid;
    uint32_t transid;
    uint64_t transtime;
    string content;
    uint32_t amount;
    string party;
    account_name primary_key() const {
      return account;
    }
    EOSLIB_SERIALIZE(profilef, (account)(type)(userid)(transid)(transtime)(content)(amount)(party))
  };
  //initialize the table                                                                                                                                                                                                                 
  typedef eosio::multi_index<N(profilesf), profilef> profile_tablef;
};

EOSIO_ABI(table, (create)(update)(remove))

```

```
#include <contracts.hpp>
void table::create(const account_name account,
                   const string& type,
                   const string& userid,
                   uint32_t transid,
                   uint64_t transtime,
                   const string& content,
                   uint32_t amount,
                   const string& party
                   ) {
  require_auth(account);
  profile_tablef profilef(_self, _self);
  profilef.emplace(account, [&](auto& p) {
      p.account = account;
      p.type = type;
      p.userid = userid;
      p.transid = transid;
      p.transtime = transtime;
      p.content = content;
      p.amount = amount;
      p.party = party;
    });
}

void table::update(const account_name account,
                   const string& type,
                   const string& userid,
                   uint32_t transid,
                   uint64_t transtime,
                   const string& content,
                   uint32_t amount,
                   const string& party
                   ) {
  require_auth(account);
  profile_tablef profilef(_self, _self);
  auto itr = profilef.find(account);

  profilef.modify(itr, account, [&](auto& p) {
      p.account = account;
      p.type = type;
      p.userid = userid;
      p.transid= transid;
      p.transtime = transtime;
      p.content= content;
      p.amount = amount;
      p.party =party;
    });
}

void table::remove(const account_name account) {
  require_auth(account);
  profile_tablef profilef(_self, _self);
  auto itr = profilef.find(account);
  eosio_assert(itr != profilef.end(), "No Profile");
  profilef.erase(itr);
}
```
