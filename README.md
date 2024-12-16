# Intermodal-Transportation-Carbon-Accounting
pragma solidity = 0.6.0;

contract Registration {
    address public Regulatory_Authority;  // 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4
    
    
    mapping(address => bool) public Trucking_Company_1; //0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2
    mapping(address => bool) public Manufacturer; //0x4B20993Bc481177ec7E8f571ceCaE8A9e22C02db
    mapping(address=> bool) public Freight_Rail_Provider; //0x78731D3Ca6b7E34aC0F824c42a7cC18A495cabaB
    mapping(address => bool) public Ocean_Carrier; // 0x617F2E2fD72FD9D5503197092aC168c91465E7f2
    mapping(address => bool) public Air_Cargo_Carrier; //0x17F6AD8Ef982297579C203069C1DbfFE4348c372
    mapping(address => bool) public Trucking_Company_2;//0x5c6B0f7Bf3E7ce046039Bd8FABdfD3f9F5021678
    mapping(address => bool) public Customer; //0x03C6FcED478cBbC9a4FAB34eF9f40767739D1Ff7 

    modifier onlyRegulatory_Authority {
        require(msg.sender == Regulatory_Authority, "Sender not authorized.");
        _;
    }

    constructor() public {
        Regulatory_Authority = msg.sender;
    }

    function registerTrucking_Company_1(address t1) external onlyRegulatory_Authority {
        require(!Trucking_Company_1[t1], "Trucking Company 1 already registered.");
        Trucking_Company_1[t1] = true;
    }

    function registerTrucking_Company_2(address t2) external onlyRegulatory_Authority {
        require(!Trucking_Company_2[t2], "Trucking Company 2 already registered.");
        Trucking_Company_2[t2] = true;
    }

    function registerManufacturer(address m) external onlyRegulatory_Authority {
        require(!Manufacturer[m], "Manufacturer already registered.");
        Manufacturer[m] = true;
    }

    function registerFreight_Rail_Provider(address f) external onlyRegulatory_Authority {
        require(!Freight_Rail_Provider[f], "Freight Rail Provider already registered.");
        Freight_Rail_Provider[f] = true;
    }

    function registerOcean_Carrier(address o) external onlyRegulatory_Authority {
        require(!Ocean_Carrier[o], "Ocean Carrier already registered.");
        Ocean_Carrier[o] = true;
    }

    function registerAir_Cargo_Carrier(address a) external onlyRegulatory_Authority {
        require(!Air_Cargo_Carrier[a], "Air Cargo Carrier already registered.");
        Air_Cargo_Carrier[a] = true;
    }

    function registerCustomer(address c) external onlyRegulatory_Authority {
        require(!Customer[c], "Customer already registered.");
        Customer[c] = true;
    }

    function isRegulatory_Authority(address r) public view returns (bool) {
        return (Regulatory_Authority == r);
    }

    function Trucking_Company_1Exists(address t1) public view returns (bool) {
        return Trucking_Company_1[t1];
    }

    function Trucking_Company_2Exists(address t2) public view returns (bool) {
        return Trucking_Company_2[t2];
    }

    function ManufacturerExists(address m) public view returns (bool) {
        return Manufacturer[m];
    }

    function Freight_Rail_ProviderExists(address f) public view returns (bool) {
        return Freight_Rail_Provider[f];
    }

    function Ocean_CarrierExists(address o) public view returns (bool) {
        return Ocean_Carrier[o];
    }

    function Air_Cargo_CarrierExists(address a) public view returns (bool) {
        return Air_Cargo_Carrier[a];
    }

    function CustomerExists(address c) public view returns (bool) {
        return Customer[c];
    }
}

contract Carbon_Accounting {
    uint public totalCO2Emitted_s;
    uint public totalCO2Emitted_a;
    uint public totalCO2Emitted_trucking1;
    uint public totalCO2Emitted_trucking2;
    uint public totalCO2Emitted_freightRail;
    uint public totalCO2Emitted_ocean;
    uint public totalCO2Emitted_air;
    uint public totalCO2Emitted_customer;

    address payable public Manufacturer;
    Registration public registrationContract;

    constructor(address registration) public {
        registrationContract = Registration(registration);

        require(registrationContract.ManufacturerExists(msg.sender), "Sender not authorized");
        Manufacturer = payable(msg.sender);
    }

    modifier onlyTrucking_Company_1 {
        require(registrationContract.Trucking_Company_1Exists(msg.sender), "Sender not authorized");
        _;
    }

    modifier onlyManufacturer {
        require(registrationContract.ManufacturerExists(msg.sender), "Sender not authorized");
        _;
    }

    modifier onlyFreight_Rail_Provider {
        require(registrationContract.Freight_Rail_ProviderExists(msg.sender), "Sender not authorized");
        _;
    }

    modifier onlyOcean_Carrier {
        require(registrationContract.Ocean_CarrierExists(msg.sender), "Sender not authorized");
        _;
    }

    modifier onlyAir_Cargo_Carrier {
        require(registrationContract.Air_Cargo_CarrierExists(msg.sender), "Sender not authorized");
        _;
    }

    modifier onlyTrucking_Company_2 {
        require(registrationContract.Trucking_Company_2Exists(msg.sender), "Sender not authorized");
        _;
    }

    modifier onlyCustomer {
        require(registrationContract.CustomerExists(msg.sender), "Sender not authorized");
        _;
    }

    event CO2EmissionsUpdated(
        address indexed stakeholder,
        uint256 scope1,
        uint256 scope2,
        uint256 scope3,
        uint256 totalEmissions,
        uint256 timestamp,
        bytes32 blockHash
    );

    function updateTrucking1CO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyTrucking_Company_1 {
        totalCO2Emitted_trucking1 += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_trucking1, block.timestamp, blockhash(block.number - 1));
    }

    function updateTrucking2CO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyTrucking_Company_2 {
        totalCO2Emitted_trucking2 += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_trucking2, block.timestamp, blockhash(block.number - 1));
    }

    function updateFreightRailCO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyFreight_Rail_Provider {
        totalCO2Emitted_freightRail += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_freightRail, block.timestamp, blockhash(block.number - 1));
    }

    function updateOceanCO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyOcean_Carrier {
        totalCO2Emitted_ocean += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_ocean, block.timestamp, blockhash(block.number - 1));
    }

    function updateAirCO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyAir_Cargo_Carrier {
        totalCO2Emitted_air += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_air, block.timestamp, blockhash(block.number - 1));
    }

    function updateCustomerCO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyCustomer {
        totalCO2Emitted_customer += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_customer, block.timestamp, blockhash(block.number - 1));
    }

    function updateManufacturerCO2Emissions(
        uint256 scope1,
        uint256 scope2,
        uint256 scope3
    ) public onlyManufacturer {
        totalCO2Emitted_s += scope1 + scope2 + scope3;
        emit CO2EmissionsUpdated(msg.sender, scope1, scope2, scope3, totalCO2Emitted_s, block.timestamp, blockhash(block.number - 1));
    }
}
