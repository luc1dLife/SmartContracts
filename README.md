## Solidity Smart Contracts

Smart contracts from various decentralized applications I've made.

![licensebadge](https://img.shields.io/badge/license-CC0_1.0_Universal-blue)

[Profile.sol](./Profile.sol) [waviii user profile storage contract]

    pragma solidity ^0.5.0;

    contract Profile {

        struct User {
           string name;
           string subtitle;
           string sdescription;
           string ldescription;
           string weburl;
           string imghash;
        }

        mapping(address => User) public users; 

      function set(string memory _name, string memory _subtitle, string memory _sdescription,string memory _ldescription, string memory _weburl, string memory _imghash) public {
        users[msg.sender] = User(_name, _subtitle, _sdescription, _ldescription, _weburl, _imghash);
      }

      function get() public view returns (string memory, string memory, string memory, string memory, string memory, string memory) {
        return (
            users[msg.sender].name, 
            users[msg.sender].subtitle, 
            users[msg.sender].sdescription, 
            users[msg.sender].ldescription, 
            users[msg.sender].weburl, 
            users[msg.sender].imghash
        );
    }}

---
[waviii.sol](./waviii.sol) [waviii ERC-20 token contract]

    pragma solidity ^0.5.0;

    contract Token {
        string  public name = "waviii";
        string  public symbol = "waviii";
        uint256 public totalSupply = 1000000000000000000000000; // 1 million tokens
        uint8   public decimals = 18;

        event Transfer(
            address indexed _from,
            address indexed _to,
            uint256 _value
        );

        event Approval(
            address indexed _owner,
            address indexed _spender,
            uint256 _value
        );

        mapping(address => uint256) public balanceOf;
        mapping(address => mapping(address => uint256)) public allowance;

        constructor() public {
            balanceOf[msg.sender] = totalSupply;
        }

        function transfer(address _to, uint256 _value) public returns (bool success) {
            require(balanceOf[msg.sender] >= _value);
            balanceOf[msg.sender] -= _value;
            balanceOf[_to] += _value;
            emit Transfer(msg.sender, _to, _value);
            return true;
        }

        function approve(address _spender, uint256 _value) public returns (bool success) {
            allowance[msg.sender][_spender] = _value;
            emit Approval(msg.sender, _spender, _value);
            return true;
        }

        function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
            require(_value <= balanceOf[_from]);
            require(_value <= allowance[_from][msg.sender]);
            balanceOf[_from] -= _value;
            balanceOf[_to] += _value;
            allowance[_from][msg.sender] -= _value;
            emit Transfer(_from, _to, _value);
            return true;
        }
    }

---
[waviii_OZ.sol](./waviii_OZ.sol) [waviii ERC-20 token contract with Openzeppelin]

    pragma solidity ^0.5.0;

    import "@openzeppelin/contracts/token/ERC20/ERC20Mintable.sol";

    contract waviii is ERC20Mintable {
      string  public name;
      string  public symbol;
      uint256 public decimals;
      string  public standard;
      string  public statement;

      constructor() public {
        name = "waviii Token";
        symbol = "waviii";
        decimals = 18;
        standard = "waviii Token v1.0";
        statement = "Be waviii.";
      }
    }

---
[wavSwap.sol](./WavSwap.sol) [waviii wavSwap contract]

    pragma solidity ^0.5.0;

    import "./waviii.sol";

    contract EthSwap {
      string public name = "wavSwap";
      Token public token;
      uint public rate = 100;

      event TokensPurchased(
        address account,
        address token,
        uint amount,
        uint rate
      );

      event TokensSold(
        address account,
        address token,
        uint amount,
        uint rate
      );

      constructor(Token _token) public {
        token = _token;
      }

      function buyTokens() public payable {
        // Calculate the number of waviii tokens to buy
        uint tokenAmount = msg.value * rate;

        // Require that WavSwap has enough tokens
        require(token.balanceOf(address(this)) >= tokenAmount);

        // Transfer waviii tokens to the user
        token.transfer(msg.sender, tokenAmount);

        // Emit an event
        emit TokensPurchased(msg.sender, address(token), tokenAmount, rate);
      }

      function sellTokens(uint _amount) public {
        // User can't sell more tokens than they have
        require(token.balanceOf(msg.sender) >= _amount);

        // Calculate the amount of Ether to redeem
        uint etherAmount = _amount / rate;

        // Require that WavSwap has enough Ether
        require(address(this).balance >= etherAmount);

        // Perform swap
        token.transferFrom(msg.sender, address(this), _amount);
        msg.sender.transfer(etherAmount);

        // Emit an event
        emit TokensSold(msg.sender, address(token), _amount, rate);
      }
    }

#### Questions

Contact me at [matt@brassey.io](mailto:matt@brassey.io) with any questions or comments.

#### License

`SmartContracts` is published under the __CC0_1.0_Universal__ license.

> The Creative Commons CC0 Public Domain Dedication waives copyright interest in a work you've created and dedicates it to the world-wide public domain. Use CC0 to opt out of copyright entirely and ensure your work has the widest reach. As with the Unlicense and typical software licenses, CC0 disclaims warranties. CC0 is very similar to the Unlicense.