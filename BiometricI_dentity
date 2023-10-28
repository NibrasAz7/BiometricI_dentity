// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BiometricIdentity {
    
    // Structures
    struct Subject {
        bytes32 ID;           // ID of the Subject as fixed-length byte array
        uint16 hx;            // Hash representation (optimized size)
        uint16 delta;         // The offset (optimized size)
    }
    
    struct Node {
        uint ID;              // Id of the Authentication Center
        string name;          // Name of the Authentication Center
        address addr;         // Ethereum address of the Center
        bool isAuthorised;    // Authorization status
        bool isEnrollment;    // Enrollment center status
    }

    // Events
    event SubjectSet(bytes32 _ID, uint16 _hx, uint16 _delta);
    event SubjectDeleted(bytes32 _ID);
    event NodeSet(uint _ID, string _name, address _addr);
    event NodeDeleted(address _addr);
    event NodeStatusUpdated(address _addr, bool isAuthorised, bool isEnrollment);

    // Private state variable for contract creator's address
    address private owner;
    
    // Mappings
    mapping(bytes32 => Subject) private subjects;
    mapping(address => Node) private nodes;

    // Modifiers
    modifier isAC() {
        require(nodes[msg.sender].isAuthorised, "Caller is not an authorized Authentication Center");
        _;
    }

    modifier isEC() {
        require(nodes[msg.sender].isEnrollment, "Caller is not an Enrollment Center");
        _;
    }
    
    // Constructor
    constructor()  {
        owner = msg.sender;
        nodes[owner] = Node({
            ID: 1,
            name: "Main Enrollment Center",
            addr: owner,
            isAuthorised: true,
            isEnrollment: true
        });
    }
    
    // Functions for Subjects
    function setSubject(bytes32 _ID, uint16 _hx, uint16 _delta) public isEC {
        subjects[_ID] = Subject(_ID, _hx, _delta);
        emit SubjectSet(_ID, _hx, _delta);
    }

    function getSubject(bytes32 _ID) public view isAC returns (bytes32, uint16, uint16) {
        Subject memory subject = subjects[_ID];
        return (subject.ID, subject.hx, subject.delta);
    }

    function deleteSubject(bytes32 _ID) public isEC {
        delete subjects[_ID];
        emit SubjectDeleted(_ID);
    }
    
    // Functions for Nodes
    function setNode(uint _ID, string memory _name, address _addr) public isEC {
        nodes[_addr] = Node(_ID, _name, _addr, true, false); // Setting as authorized but not an enrollment center by default
        emit NodeSet(_ID, _name, _addr);
    }

    function getNode(address _addr) public view isEC returns (uint, string memory, address, bool, bool) {
        Node memory node = nodes[_addr];
        return (node.ID, node.name, node.addr, node.isAuthorised, node.isEnrollment);
    }

    function deleteNode(address _addr) public isEC {
        delete nodes[_addr];
        emit NodeDeleted(_addr);
    }

    function updateNodeStatus(address _addr, bool _isAuthorised, bool _isEnrollment) public isEC {
        Node storage node = nodes[_addr];
        node.isAuthorised = _isAuthorised;
        node.isEnrollment = _isEnrollment;
        emit NodeStatusUpdated(_addr, _isAuthorised, _isEnrollment);
    }
}
