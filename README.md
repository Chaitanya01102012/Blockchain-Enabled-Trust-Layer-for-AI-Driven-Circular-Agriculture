// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AgricultureLog {
    enum EventType { PlantDisease, Intervention, BioEnzymeBatch, DecompositionResult }

    struct AgriEvent {
        EventType eventType;
        string   experimentId;   // or batchId
        bytes32  dataHash;       // SHA-256 hash of off-chain JSON
        uint256  timestamp;      // block timestamp when logged
        address  recordedBy;
    }

    AgriEvent[] public events;

    event EventRecorded(
        uint256 index,
        EventType eventType,
        string experimentId,
        bytes32 dataHash,
        uint256 timestamp,
        address recordedBy
    );

    function recordEvent(
        EventType _eventType,
        string calldata _experimentId,
        bytes32 _dataHash
    ) external {
        AgriEvent memory e = AgriEvent({
            eventType: _eventType,
            experimentId: _experimentId,
            dataHash: _dataHash,
            timestamp: block.timestamp,
            recordedBy: msg.sender
        });

        events.push(e);
        emit EventRecorded(events.length - 1, _eventType, _experimentId, _dataHash, block.timestamp, msg.sender);
    }

    function getEvent(uint256 index) external view returns (AgriEvent memory) {
        require(index < events.length, "Invalid index");
        return events[index];
    }

    function getEventCount() external view returns (uint256) {
        return events.length;
    }
}
